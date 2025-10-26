<template>
  <div class="dashboard-container">
    <!-- Header -->
    <header class="dashboard-header">
      <div class="content-wrapper">
        <div class="header-content">
          <h1>TicketFlow Dashboard</h1>
          <button @click="handleLogout" class="btn btn-logout">
            Logout
          </button>
        </div>
      </div>
    </header>

    <!-- Main Content -->
    <main class="dashboard-main">
      <div class="content-wrapper">
        <h2>Ticket Statistics</h2>
        
        <!-- Stats Grid -->
        <div class="stats-grid">
          <div class="stat-box stat-total">
            <div class="stat-icon">📊</div>
            <div class="stat-info">
              <h3>{{ stats.total }}</h3>
              <p>Total Tickets</p>
            </div>
          </div>

          <div class="stat-box stat-open">
            <div class="stat-icon">🟢</div>
            <div class="stat-info">
              <h3>{{ stats.open }}</h3>
              <p>Open Tickets</p>
            </div>
          </div>

          <div class="stat-box stat-progress">
            <div class="stat-icon">🟡</div>
            <div class="stat-info">
              <h3>{{ stats.inProgress }}</h3>
              <p>In Progress</p>
            </div>
          </div>

          <div class="stat-box stat-closed">
            <div class="stat-icon">⚫</div>
            <div class="stat-info">
              <h3>{{ stats.closed }}</h3>
              <p>Resolved Tickets</p>
            </div>
          </div>
        </div>

        <!-- Quick Actions -->
        <div class="quick-actions">
          <h2>Quick Actions</h2>
          <div class="action-buttons">
            <router-link to="/tickets" class="btn btn-primary">
              Manage Tickets
            </router-link>
            <button @click="$router.push('/tickets')" class="btn btn-secondary">
              Create New Ticket
            </button>
          </div>
        </div>
      </div>
    </main>

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
import { getTickets } from '../utils/storage';

export default {
  name: 'Dashboard',
  data() {
    return {
      stats: {
        total: 0,
        open: 0,
        inProgress: 0,
        closed: 0
      }
    }
  },
  mounted() {
    this.loadStats();
  },
  methods: {
    loadStats() {
      const tickets = getTickets();
      this.stats = {
        total: tickets.length,
        open: tickets.filter(t => t.status === 'open').length,
        inProgress: tickets.filter(t => t.status === 'in_progress').length,
        closed: tickets.filter(t => t.status === 'closed').length
      };
    },
    handleLogout() {
      logout();
      this.$router.push('/');
    }
  }
}
</script>

<style scoped>
.dashboard-container {
  min-height: 100vh;
  background: #f9fafb;
}

.dashboard-header {
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

.dashboard-header h1 {
  font-size: 2rem;
  margin: 0;
}

.btn-logout {
  background: rgba(255, 255, 255, 0.2);
  color: white;
  border: 2px solid white;
  padding: 10px 24px;
  border-radius: 8px;
  cursor: pointer;
  font-weight: 600;
  transition: all 0.3s;
}

.btn-logout:hover {
  background: white;
  color: #667eea;
}

.dashboard-main {
  padding: 60px 20px;
}

.dashboard-main h2 {
  margin-bottom: 30px;
  color: #1f2937;
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 25px;
  margin-bottom: 60px;
}

.stat-box {
  background: white;
  padding: 30px;
  border-radius: 12px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  display: flex;
  align-items: center;
  gap: 20px;
  transition: transform 0.3s;
}

.stat-box:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 15px rgba(0, 0, 0, 0.15);
}

.stat-icon {
  font-size: 3rem;
}

.stat-info h3 {
  font-size: 2.5rem;
  margin: 0 0 5px 0;
  color: #1f2937;
}

.stat-info p {
  margin: 0;
  color: #6b7280;
  font-weight: 600;
}

.stat-total {
  border-left: 5px solid #3b82f6;
}

.stat-open {
  border-left: 5px solid #10b981;
}

.stat-progress {
  border-left: 5px solid #f59e0b;
}

.stat-closed {
  border-left: 5px solid #6b7280;
}

.quick-actions {
  background: white;
  padding: 40px;
  border-radius: 12px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.quick-actions h2 {
  margin-bottom: 20px;
}

.action-buttons {
  display: flex;
  gap: 15px;
  flex-wrap: wrap;
}

@media (max-width: 768px) {
  .header-content {
    flex-direction: column;
    gap: 15px;
  }

  .stats-grid {
    grid-template-columns: 1fr;
  }

  .action-buttons {
    flex-direction: column;
  }

  .action-buttons .btn {
    width: 100%;
  }
}
</style>
