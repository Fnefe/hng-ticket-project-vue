<template>
  <div class="tickets-container">
    <!-- Header -->
    <header class="tickets-header">
      <div class="content-wrapper">
        <div class="header-content">
          <div>
            <h1>Ticket Management</h1>
            <button @click="$router.push('/dashboard')" class="btn-back">
              ← Back to Dashboard
            </button>
          </div>
          <button @click="handleLogout" class="btn btn-logout">
            Logout
          </button>
        </div>
      </div>
    </header>

    <!-- Main Content -->
    <main class="tickets-main">
      <div class="content-wrapper">
        <!-- Create Button -->
        <div v-if="!showForm" class="create-section">
          <button @click="showForm = true" class="btn btn-primary btn-create">
            + Create New Ticket
          </button>
        </div>

        <!-- Ticket Form -->
        <div v-if="showForm" class="ticket-form-container">
          <div class="form-header">
            <h2>{{ editingTicket ? 'Edit Ticket' : 'Create New Ticket' }}</h2>
            <button @click="resetForm" class="btn-close">✕</button>
          </div>

          <form @submit.prevent="handleSubmit">
            <div class="form-group">
              <label>Title *</label>
              <input
                type="text"
                v-model="formData.title"
                :class="{ error: errors.title }"
                placeholder="Enter ticket title"
                @input="clearError('title')"
              />
              <span v-if="errors.title" class="error-message">{{ errors.title }}</span>
            </div>

            <div class="form-group">
              <label>Description</label>
              <textarea
                v-model="formData.description"
                :class="{ error: errors.description }"
                placeholder="Enter ticket description (optional)"
                rows="4"
                @input="clearError('description')"
              />
              <span v-if="errors.description" class="error-message">{{ errors.description }}</span>
            </div>

            <div class="form-row">
              <div class="form-group">
                <label>Status *</label>
                <select
                  v-model="formData.status"
                  :class="{ error: errors.status }"
                  @change="clearError('status')"
                >
                  <option value="open">Open</option>
                  <option value="in_progress">In Progress</option>
                  <option value="closed">Closed</option>
                </select>
                <span v-if="errors.status" class="error-message">{{ errors.status }}</span>
              </div>

              <div class="form-group">
                <label>Priority</label>
                <select v-model="formData.priority">
                  <option value="low">Low</option>
                  <option value="medium">Medium</option>
                  <option value="high">High</option>
                </select>
              </div>
            </div>

            <div class="form-actions">
              <button type="submit" class="btn btn-primary">
                {{ editingTicket ? 'Update Ticket' : 'Create Ticket' }}
              </button>
              <button type="button" @click="resetForm" class="btn btn-secondary">
                Cancel
              </button>
            </div>
          </form>
        </div>

        <!-- Tickets List -->
        <div class="tickets-list">
          <h2>All Tickets ({{ tickets.length }})</h2>
          
          <div v-if="tickets.length === 0" class="empty-state">
            <p>No tickets yet. Create your first ticket to get started!</p>
          </div>

          <div v-else class="tickets-grid">
            <div v-for="ticket in tickets" :key="ticket.id" class="ticket-card">
              <div class="ticket-header">
                <h3>{{ ticket.title }}</h3>
                <span :class="`status-badge ${getStatusColor(ticket.status)}`">
                  {{ getStatusLabel(ticket.status) }}
                </span>
              </div>

              <p v-if="ticket.description" class="ticket-description">
                {{ ticket.description }}
              </p>

              <div class="ticket-meta">
                <span :class="`priority priority-${ticket.priority || 'medium'}`">
                  Priority: {{ ticket.priority || 'medium' }}
                </span>
                <span class="ticket-date">
                  {{ formatDate(ticket.createdAt) }}
                </span>
              </div>

              <div class="ticket-actions">
                <button @click="handleEdit(ticket)" class="btn btn-edit">
                  Edit
                </button>
                <button @click="handleDeleteClick(ticket)" class="btn btn-delete">
                  Delete
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </main>

    <!-- Delete Confirmation Modal -->
    <div v-if="deleteConfirm" class="modal-overlay">
      <div class="modal">
        <h3>Confirm Delete</h3>
        <p>Are you sure you want to delete "{{ deleteConfirm.title }}"?</p>
        <p class="warning">This action cannot be undone.</p>
        <div class="modal-actions">
          <button @click="confirmDelete" class="btn btn-danger">
            Delete
          </button>
          <button @click="deleteConfirm = null" class="btn btn-secondary">
            Cancel
          </button>
        </div>
      </div>
    </div>

    <!-- Toast Notification -->
    <div v-if="toast" :class="`toast toast-${toast.type}`">
      {{ toast.message }}
    </div>

    <!-- Footer -->
    <footer class="footer">
      <div class="content-wrapper">
        <p>&copy; 2025 TicketFlow. Built for HNG Internship.</p>
      </div>
    </footer>
  </div>
</template>

<script>
import { logout } from '../utils/auth';
import { getTickets, addTicket, updateTicket, deleteTicket } from '../utils/storage';

export default {
  name: 'TicketManagement',
  data() {
    return {
      tickets: [],
      showForm: false,
      editingTicket: null,
      formData: {
        title: '',
        description: '',
        status: 'open',
        priority: 'medium'
      },
      errors: {},
      toast: null,
      deleteConfirm: null
    }
  },
  mounted() {
    this.loadTickets();
  },
  methods: {
    loadTickets() {
      this.tickets = getTickets();
    },
    clearError(field) {
      if (this.errors[field]) {
        this.errors = { ...this.errors, [field]: '' };
      }
    },
    validate() {
      const newErrors = {};
      if (!this.formData.title.trim()) {
        newErrors.title = 'Title is required';
      }
      if (!this.formData.status) {
        newErrors.status = 'Status is required';
      }
      if (!['open', 'in_progress', 'closed'].includes(this.formData.status)) {
        newErrors.status = 'Status must be open, in_progress, or closed';
      }
      if (this.formData.description && this.formData.description.length > 500) {
        newErrors.description = 'Description must be less than 500 characters';
      }
      return newErrors;
    },
    handleSubmit() {
      const newErrors = this.validate();

      if (Object.keys(newErrors).length > 0) {
        this.errors = newErrors;
        this.showToast('error', 'Please fix the errors in the form');
        return;
      }

      try {
        if (this.editingTicket) {
          updateTicket(this.editingTicket.id, this.formData);
          this.showToast('success', 'Ticket updated successfully!');
        } else {
          addTicket(this.formData);
          this.showToast('success', 'Ticket created successfully!');
        }

        this.resetForm();
        this.loadTickets();
      } catch (error) {
        this.showToast('error', 'Failed to save ticket. Please try again.');
      }
    },
    handleEdit(ticket) {
      this.editingTicket = ticket;
      this.formData = {
        title: ticket.title,
        description: ticket.description || '',
        status: ticket.status,
        priority: ticket.priority || 'medium'
      };
      this.showForm = true;
      window.scrollTo({ top: 0, behavior: 'smooth' });
    },
    handleDeleteClick(ticket) {
      this.deleteConfirm = ticket;
    },
    confirmDelete() {
      try {
        deleteTicket(this.deleteConfirm.id);
        this.showToast('success', 'Ticket deleted successfully!');
        this.deleteConfirm = null;
        this.loadTickets();
      } catch (error) {
        this.showToast('error', 'Failed to delete ticket. Please try again.');
      }
    },
    resetForm() {
      this.formData = {
        title: '',
        description: '',
        status: 'open',
        priority: 'medium'
      };
      this.editingTicket = null;
      this.showForm = false;
      this.errors = {};
    },
    showToast(type, message) {
      this.toast = { type, message };
      setTimeout(() => this.toast = null, 3000);
    },
    handleLogout() {
      logout();
      this.$router.push('/');
    },
    getStatusColor(status) {
      switch (status) {
        case 'open': return 'status-open';
        case 'in_progress': return 'status-progress';
        case 'closed': return 'status-closed';
        default: return '';
      }
    },
    getStatusLabel(status) {
      switch (status) {
        case 'in_progress': return 'In Progress';
        case 'open': return 'Open';
        case 'closed': return 'Closed';
        default: return status;
      }
    },
    formatDate(dateString) {
      return new Date(dateString).toLocaleDateString();
    }
  }
}
</script>

<style scoped>
.tickets-container {
  min-height: 100vh;
  background: #f9fafb;
}

.tickets-header {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  padding: 30px 20px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.header-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.tickets-header h1 {
  font-size: 2rem;
  margin: 0;
}

.btn-back {
  background: transparent;
  border: none;
  color: white;
  cursor: pointer;
  font-size: 0.9rem;
  margin-top: 10px;
  padding: 5px 0;
  transition: opacity 0.3s;
}

.btn-back:hover {
  opacity: 0.8;
}

.tickets-main {
  padding: 40px 20px;
}

.create-section {
  margin-bottom: 40px;
}

.btn-create {
  font-size: 1.1rem;
  padding: 14px 30px;
}

.ticket-form-container {
  background: white;
  padding: 30px;
  border-radius: 12px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  margin-bottom: 40px;
}

.form-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 25px;
}

.form-header h2 {
  margin: 0;
  color: #1f2937;
}

.btn-close {
  background: transparent;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
  color: #6b7280;
  padding: 0;
  width: 30px;
  height: 30px;
  transition: color 0.3s;
}

.btn-close:hover {
  color: #ef4444;
}

.form-group {
  margin-bottom: 20px;
}

.form-group label {
  display: block;
  margin-bottom: 8px;
  font-weight: 600;
  color: #374151;
}

.form-group input,
.form-group textarea,
.form-group select {
  width: 100%;
  padding: 12px;
  border: 2px solid #e5e7eb;
  border-radius: 8px;
  font-size: 1rem;
  transition: border-color 0.3s;
}

.form-group input:focus,
.form-group textarea:focus,
.form-group select:focus {
  outline: none;
  border-color: #667eea;
}

.form-group input.error,
.form-group textarea.error,
.form-group select.error {
  border-color: #ef4444;
}

.error-message {
  color: #ef4444;
  font-size: 0.875rem;
  margin-top: 5px;
  display: block;
}

.form-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
}

textarea {
  resize: vertical;
  font-family: inherit;
}

.form-actions {
  display: flex;
  gap: 15px;
  margin-top: 25px;
}

.tickets-list h2 {
  margin-bottom: 25px;
  color: #1f2937;
}

.empty-state {
  background: white;
  padding: 60px 20px;
  border-radius: 12px;
  text-align: center;
  color: #6b7280;
}

.tickets-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
  gap: 25px;
}

.ticket-card {
  background: white;
  padding: 25px;
  border-radius: 12px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s, box-shadow 0.3s;
}

.ticket-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 15px rgba(0, 0, 0, 0.15);
}

.ticket-header {
  display: flex;
  justify-content: space-between;
  align-items: start;
  margin-bottom: 15px;
  gap: 10px;
}

.ticket-header h3 {
  margin: 0;
  color: #1f2937;
  font-size: 1.2rem;
  flex: 1;
}

.status-badge {
  padding: 5px 12px;
  border-radius: 20px;
  font-size: 0.85rem;
  font-weight: 600;
  white-space: nowrap;
}

.status-open {
  background: #d1fae5;
  color: #065f46;
}

.status-progress {
  background: #fef3c7;
  color: #92400e;
}

.status-closed {
  background: #e5e7eb;
  color: #374151;
}

.ticket-description {
  color: #6b7280;
  margin: 15px 0;
  line-height: 1.6;
}

.ticket-meta {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 15px 0;
  padding-top: 15px;
  border-top: 1px solid #e5e7eb;
  font-size: 0.9rem;
}

.priority {
  font-weight: 600;
  text-transform: capitalize;
}

.priority-high {
  color: #dc2626;
}

.priority-medium {
  color: #f59e0b;
}

.priority-low {
  color: #10b981;
}

.ticket-date {
  color: #9ca3af;
}

.ticket-actions {
  display: flex;
  gap: 10px;
  margin-top: 15px;
}

.btn-edit {
  flex: 1;
  background: #3b82f6;
  color: white;
  border: none;
  padding: 10px;
  border-radius: 8px;
  cursor: pointer;
  font-weight: 600;
  transition: background 0.3s;
}

.btn-edit:hover {
  background: #2563eb;
}

.btn-delete {
  flex: 1;
  background: #ef4444;
  color: white;
  border: none;
  padding: 10px;
  border-radius: 8px;
  cursor: pointer;
  font-weight: 600;
  transition: background 0.3s;
}

.btn-delete:hover {
  background: #dc2626;
}

.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal {
  background: white;
  padding: 30px;
  border-radius: 12px;
  max-width: 450px;
  width: 90%;
}

.modal h3 {
  margin: 0 0 15px 0;
  color: #1f2937;
}

.modal p {
  color: #6b7280;
  margin: 10px 0;
}

.warning {
  color: #ef4444;
  font-weight: 600;
}

.modal-actions {
  display: flex;
  gap: 15px;
  margin-top: 25px;
}

.btn-danger {
  flex: 1;
  background: #ef4444;
  color: white;
  border: none;
  padding: 12px 24px;
  border-radius: 8px;
  cursor: pointer;
  font-weight: 600;
  transition: background 0.3s;
}

.btn-danger:hover {
  background: #dc2626;
}

.toast {
  position: fixed;
  top: 20px;
  right: 20px;
  padding: 15px 20px;
  border-radius: 8px;
  color: white;
  font-weight: 600;
  animation: slideIn 0.3s ease;
  z-index: 1000;
}

.toast-success {
  background: #10b981;
}

.toast-error {
  background: #ef4444;
}

@keyframes slideIn {
  from {
    transform: translateX(100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

@media (max-width: 768px) {
  .header-content {
    flex-direction: column;
    gap: 15px;
    align-items: flex-start;
  }

  .form-row {
    grid-template-columns: 1fr;
  }

  .form-actions {
    flex-direction: column;
  }

  .form-actions .btn {
    width: 100%;
  }

  .tickets-grid {
    grid-template-columns: 1fr;
  }

  .modal-actions {
    flex-direction: column;
  }

  .modal-actions .btn {
    width: 100%;
  }
}
</style>