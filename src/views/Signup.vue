<template>
  <div class="auth-container">
    <div class="auth-box">
      <h2>Create Account</h2>
      <p class="subtitle">Sign up to start managing tickets</p>
      
      <form @submit.prevent="handleSubmit">
        <div class="form-group">
          <label>Full Name</label>
          <input
            type="text"
            v-model="formData.name"
            :class="{ error: errors.name }"
            @input="clearError('name')"
          />
          <span v-if="errors.name" class="error-message">{{ errors.name }}</span>
        </div>

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

        <div class="form-group">
          <label>Confirm Password</label>
          <input
            type="password"
            v-model="formData.confirmPassword"
            :class="{ error: errors.confirmPassword }"
            @input="clearError('confirmPassword')"
          />
          <span v-if="errors.confirmPassword" class="error-message">{{ errors.confirmPassword }}</span>
        </div>

        <button type="submit" class="btn btn-primary full-width">
          Sign Up
        </button>
      </form>

      <p class="auth-footer">
        Already have an account? <router-link to="/login">Login</router-link>
      </p>
    </div>

    <div v-if="toast" :class="`toast toast-${toast.type}`">
      {{ toast.message }}
    </div>
  </div>
</template>

<script>
import { signup } from '../utils/auth';

export default {
  name: 'Signup',
  data() {
    return {
      formData: {
        name: '',
        email: '',
        password: '',
        confirmPassword: ''
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
      if (!this.formData.name) newErrors.name = 'Name is required';
      if (!this.formData.email) newErrors.email = 'Email is required';
      if (!this.formData.password) newErrors.password = 'Password is required';
      if (this.formData.password.length < 6) {
        newErrors.password = 'Password must be at least 6 characters';
      }
      if (this.formData.password !== this.formData.confirmPassword) {
        newErrors.confirmPassword = 'Passwords do not match';
      }
      return newErrors;
    },
    handleSubmit() {
      const newErrors = this.validate();
      
      if (Object.keys(newErrors).length > 0) {
        this.errors = newErrors;
        return;
      }

      const result = signup(this.formData.email, this.formData.password, this.formData.name);
      
      if (result.success) {
        this.showToast('success', 'Account created successfully!');
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
/* Same styles as Login.vue */
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

.form-group input {
  width: 100%;
  padding: 12px;
  border: 2px solid #e5e7eb;
  border-radius: 8px;
  font-size: 1rem;
  transition: border-color 0.3s;
}

.form-group input:focus {
  outline: none;
  border-color: #667eea;
}

.form-group input.error {
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
