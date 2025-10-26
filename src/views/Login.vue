<template>
  <div class="auth-container">
    <div class="auth-box">
      <h2>Welcome Back</h2>
      <p class="subtitle">Login to manage your tickets</p>
      
      <form @submit.prevent="handleSubmit">
        <div class="form-group">
          <label>Email</label>
          <input
            type="email"
            v-model="formData.email"
            :class="{ error: errors.email }"
            @input="clearError('email')"
          />
          <span v-if="errors.email" class="error-message">{{ errors.email }}</span>
        </div>

        <div class="form-group">
          <label>Password</label>
          <input
            type="password"
            v-model="formData.password"
            :class="{ error: errors.password }"
            @input="clearError('password')"
          />
          <span v-if="errors.password" class="error-message">{{ errors.password }}</span>
        </div>

        <button type="submit" class="btn btn-primary full-width">
          Login
        </button>
      </form>

      <p class="auth-footer">
        Don't have an account? <router-link to="/signup">Sign up</router-link>
      </p>

      <div class="test-credentials">
        <small>Test credentials: user@test.com / password123</small>
      </div>
    </div>

    <div v-if="toast" :class="`toast toast-${toast.type}`">
      {{ toast.message }}
    </div>
  </div>
</template>

<script>
import { login } from '../utils/auth';

export default {
  name: 'Login',
  data() {
    return {
      formData: {
        email: '',
        password: ''
      },
      errors: {},
      toast: null
    }
  },
  methods: {
    clearError(field) {
      if (this.errors[field]) {
        this.errors = { ...this.errors, [field]: '' };
      }
    },
    validate() {
      const newErrors = {};
      if (!this.formData.email) newErrors.email = 'Email is required';
      if (!this.formData.password) newErrors.password = 'Password is required';
      return newErrors;
    },
    handleSubmit() {
      const newErrors = this.validate();
      
      if (Object.keys(newErrors).length > 0) {
        this.errors = newErrors;
        return;
      }

      const result = login(this.formData.email, this.formData.password);
      
      if (result.success) {
        this.showToast('success', 'Login successful!');
        setTimeout(() => this.$router.push('/dashboard'), 1000);
      } else {
        this.showToast('error', result.error);
      }
    },
    showToast(type, message) {
      this.toast = { type, message };
      setTimeout(() => this.toast = null, 3000);
    }
  }
}
</script>

<style scoped>
.auth-container {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  padding: 20px;
}

.auth-box {
  background: white;
  padding: 40px;
  border-radius: 12px;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.2);
  width: 100%;
  max-width: 450px;
}

.auth-box h2 {
  margin-bottom: 10px;
  color: #1f2937;
}

.subtitle {
  color: #6b7280;
  margin-bottom: 30px;
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

.full-width {
  width: 100%;
}

.auth-footer {
  text-align: center;
  margin-top: 20px;
  color: #6b7280;
}

.auth-footer a {
  color: #667eea;
  text-decoration: none;
  font-weight: 600;
}

.test-credentials {
  text-align: center;
  margin-top: 20px;
  padding: 10px;
  background: #f3f4f6;
  border-radius: 6px;
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
</style>