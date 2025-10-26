<template>
  <div class="landing-container">
    <!-- Hero Section -->
    <section class="hero">
      <div class="decorative-circle circle-1"></div>
      <div class="content-wrapper">
        <h1>TicketFlow</h1>
        <p>Manage your support tickets seamlessly across teams</p>
        <div class="cta-buttons">
          <router-link to="/login" class="btn btn-primary">Login</router-link>
          <router-link to="/signup" class="btn btn-secondary">Get Started</router-link>
        </div>
      </div>
      <!-- Wave SVG -->
      <div class="wave-container">
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1440 320">
          <path fill="#6366f1" fill-opacity="0.3" d="M0,96L48,112C96,128,192,160,288,165.3C384,171,480,149,576,133.3C672,117,768,107,864,122.7C960,139,1056,181,1152,186.7C1248,192,1344,160,1392,144L1440,128L1440,320L1392,320C1344,320,1248,320,1152,320C1056,320,960,320,864,320C768,320,672,320,576,320C480,320,384,320,288,320C192,320,96,320,48,320L0,320Z"></path>
        </svg>
      </div>
    </section>

    <!-- Features Section -->
    <section class="features">
      <div class="content-wrapper">
        <div class="decorative-circle circle-2"></div>
        <h2>Features</h2>
        <div class="feature-grid">
          <div class="feature-box">
            <h3>Create Tickets</h3>
            <p>Quickly create and assign support tickets</p>
          </div>
          <div class="feature-box">
            <h3>Track Progress</h3>
            <p>Monitor ticket status in real-time</p>
          </div>
          <div class="feature-box">
            <h3>Team Collaboration</h3>
            <p>Work together to resolve issues faster</p>
          </div>
        </div>
      </div>
    </section>

    <!-- Footer -->
    <footer class="footer">
      <div class="content-wrapper">
        <p>&copy; 2025 TicketFlow. Built for HNG Internship.</p>
      </div>
    </footer>
  </div>
</template>

<script>
export default {
  name: 'Landing'
}
</script>

<style scoped>
.landing-container {
  width: 100%;
  min-height: 100vh;
}

.hero {
  position: relative;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  padding: 100px 20px 150px;
  text-align: center;
  overflow: hidden;
}

.decorative-circle {
  position: absolute;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.1);
}

.circle-1 {
  width: 300px;
  height: 300px;
  top: -100px;
  right: -50px;
}

.circle-2 {
  width: 200px;
  height: 200px;
  bottom: 50px;
  left: -50px;
}

.hero h1 {
  font-size: 3rem;
  margin-bottom: 20px;
}

.hero p {
  font-size: 1.2rem;
  margin-bottom: 30px;
}

.cta-buttons {
  display: flex;
  gap: 20px;
  justify-content: center;
}

.btn {
  padding: 12px 30px;
  border-radius: 8px;
  text-decoration: none;
  font-weight: 600;
  transition: transform 0.2s;
}

.btn:hover {
  transform: translateY(-2px);
}

.btn-primary {
  background: white;
  color: #667eea;
}

.btn-secondary {
  background: transparent;
  color: white;
  border: 2px solid white;
}

.wave-container {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
}

.wave-container svg {
  display: block;
  width: 100%;
  height: auto;
}

.features {
  position: relative;
  padding: 80px 20px;
  background: #f9fafb;
}

.features h2 {
  text-align: center;
  font-size: 2.5rem;
  margin-bottom: 50px;
}

.feature-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 30px;
}

.feature-box {
  background: white;
  padding: 30px;
  border-radius: 12px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s;
}

.feature-box:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 15px rgba(0, 0, 0, 0.15);
}

@media (max-width: 768px) {
  .hero h1 {
    font-size: 2rem;
  }
  
  .cta-buttons {
    flex-direction: column;
  }
  
  .circle-1, .circle-2 {
    display: none;
  }
}
</style>
