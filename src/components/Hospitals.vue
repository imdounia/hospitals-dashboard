<template>
  <div>
    <ul class="nav nav-tabs">
      <li class="nav-item" v-for="(hospital, index) in hospitals" :key="hospital.name">
        <a :class="['nav-link', { active: currentTab === index }]" 
           @click="currentTab = index">
          {{ hospital.name }}
        </a>
      </li>
    </ul>

    <div class="tab-content" v-if="hospitals.length > 0">
      <div class="tab-pane fade" :class="{ show: currentTab === index, active: currentTab === index }" 
           v-for="(hospital, index) in hospitals" :key="'content-' + hospital.name">
        <div class="custom-card card">
          <div class="container my-4">
          <div class="card-header fs-2 pb-0">
            {{ hospital.name }}
          </div>
          <div class="card-body">
              <div class="d-flex align-items-center card-title">      
                  <span class="material-symbols-outlined fs-6 me-1">
                    location_on
                    </span>
                    <div>{{ hospital.location }}</div>
                </div>
          </div>
          
          <div class="row justify-content-center">
            <div class="hospital-overview p-2 col-12 col-md-6 col-lg-2 m-3 d-flex align-items-center flex-column justify-content-between">
              <h5 class="card-title align-self-start fs-6">Total Patients</h5>
              <p class="card-text align-self-end fs-2">{{ hospital.overview.totalPatients }}</p> 
            </div>
            <div class="hospital-overview p-2 col-12 col-md-6 col-lg-2 m-3 d-flex align-items-center flex-column justify-content-between">
              <h5 class="card-title align-self-start fs-6">Satisfaction Rate</h5>
              <p class="card-text align-self-end fs-2">{{ hospital.overview.satisfactionRate }}</p>
            </div>
            <div class="hospital-overview p-2 col-12 col-md-6 col-lg-2 m-3 d-flex align-items-center flex-column justify-content-between">
              <h5 class="card-title align-self-start fs-6">Total Treatments</h5>
              <p class="card-text align-self-end fs-2">{{ hospital.overview.totalTreatments }}</p>
            </div>
            <div class="hospital-overview p-2 col-12 col-md-6 col-lg-2 m-3 d-flex align-items-center flex-column justify-content-between">
              <h5 class="card-title align-self-start fs-6">Number of Doctors</h5>
              <p class="card-text align-self-end fs-2">{{ hospital.overview.numberOfDoctors }}</p>
            </div>
            <div class="hospital-overview p-2 col-12 col-md-6 col-lg-2 m-3 d-flex align-items-center flex-column justify-content-between">
              <h5 class="card-title align-self-start fs-6">Number of Nurses</h5>
              <p class="card-text align-self-end fs-2">{{ hospital.overview.numberOfNurses }}</p>
            </div>
          </div> 
        
        
        <div class="custom-container my-4 mx-3">
        <div class="container">
            <canvas :id="'hospitalChart-' + index"></canvas>
          </div>
</div>

</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import '@/assets/styles.css';
import hospitalData from '@/data/data_exemple1.JSON';
import { Chart, BarController, BarElement, CategoryScale, LinearScale, Title, Tooltip, Legend } from 'chart.js';

Chart.register(BarController, BarElement, CategoryScale, LinearScale, Title, Tooltip, Legend);

export default {
    name:'hospitals',
  data() {
    return {
      hospitals: hospitalData,
      currentTab: 0 
    };
  },
  mounted() {
    this.renderChart(this.hospitals[this.currentTab], this.currentTab);
  },
  watch: {
    currentTab(newTab) {
      this.$nextTick(() => {
        this.renderChart(this.hospitals[newTab], newTab);
      });
    }
  },
  methods: {
    renderChart(hospital, index) {
      const ctx = document.getElementById(`hospitalChart-${index}`).getContext('2d');
      const labels = hospital.monthlyHospitalizations.map(item => `${item.month} ${item.year}`);
      const data = hospital.monthlyHospitalizations.map(item => item.value);

      const gradient = ctx.createLinearGradient(0, 0, ctx.canvas.width, 0);
      gradient.addColorStop(0, '#CF55F4');
      gradient.addColorStop(1, '#7E3BFA');

      if (this.chart) {
        this.chart.destroy();
      }

      this.chart = new Chart(ctx, {
        type: 'bar',
        data: {
          labels: labels,
          datasets: [
            {
              label: 'Monthly Hospitalizations',
              data: data,
              backgroundColor: gradient,
            }
          ]
        },
        options: {
          responsive: true,
          scales: {
            y: {
              beginAtZero: true
            }
          },
          plugins: {
            legend: {
              display: true
            },
            title: {
              display: true,
              text: `Monthly Hospitalizations - ${hospital.name}`
            }
          }
        }
      });
    }
  }
}
</script>

<style scoped>
/* canvas {
  max-width: 100%;
  max-height: 2000px;
} */
/* @media (max-width: 600px) {
  canvas {
    max-height: 1500px; 
  }
} */

.custom-card {
  background: var(--background-card);
  color: var(--text-color);
}

.custom-container{
  background-color: var(--dark-blue);
  border-radius: 20px;
  padding: 15px;
  box-shadow: rgba(0, 0, 0, 0.19) 0px 10px 20px, rgba(0, 0, 0, 0.23) 0px 6px 6px;
  transition: transform 0.3s ease-in-out; 
}
.custom-container:hover {
  transform: scale(1.02); /* Slight scaling on hover */
}

.hospital-overview {
  background-color: var(--dark-blue);
  height: 8rem;
  border-radius: 20px;
  box-shadow: rgba(0, 0, 0, 0.19) 0px 10px 20px, rgba(0, 0, 0, 0.23) 0px 6px 6px;
  transition: transform 0.3s ease-in-out; 
  /* padding: 20px;
  border: 1px solid #ccc; */
}
.hospital-overview:hover {
  transform: scale(1.03); /* Slight scaling on hover */
}

.hospital-overview:nth-child(odd) {
  color: var(--primary-color);
}
.hospital-overview:nth-child(even) {
  color: var(--secondary-color);
}

/* .card-title {
  color: #B18CFE;
} */

/* .button-primary {
  background-color: #EE7DFF;
  color: var(--text-color);
} */

.nav-link {
  color: var(--text-color);
  border: none;
}

.nav-link:hover, .nav-link:focus {
  color: var(--text-color);
  border-color: var(--text-color);
}

.nav-item .active{
  background: var(--gradient-color);
  border: none;
}
</style>