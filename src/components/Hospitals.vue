<template>
  <div>
    <h1 class="title mb-3 text-center"><span class="material-symbols-outlined fs-2 me-2">
      ecg_heart
      </span>Hospital dashboard
    </h1>
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
          
          <div class="row justify-content-center mx-3">
            <div class="hospital-overview p-2 col-12 col-sm-6 col-lg-2 m-3 d-flex align-items-center flex-column justify-content-between">
              <h5 class="card-title align-self-start fs-6">Total Patients</h5>
              <p class="card-text align-self-end fs-2">{{ hospital.overview.totalPatients }}</p> 
            </div>
            <div class="hospital-overview p-2 col-12 col-sm-6 col-lg-2 m-3 d-flex align-items-center flex-column justify-content-between">
              <h5 class="card-title align-self-start fs-6">Satisfaction Rate</h5>
              <p class="card-text align-self-end fs-2">{{ hospital.overview.satisfactionRate }}</p>
            </div>
            <div class="hospital-overview p-2 col-12 col-sm-6 col-lg-2 m-3 d-flex align-items-center flex-column justify-content-between">
              <h5 class="card-title align-self-start fs-6">Total Treatments</h5>
              <p class="card-text align-self-end fs-2">{{ hospital.overview.totalTreatments }}</p>
            </div>
            <div class="hospital-overview p-2 col-12 col-sm-6 col-lg-2 m-3 d-flex align-items-center flex-column justify-content-between">
              <h5 class="card-title align-self-start fs-6">Number of Doctors</h5>
              <p class="card-text align-self-end fs-2">{{ hospital.overview.numberOfDoctors }}</p>
            </div>
            <div class="hospital-overview p-2 col-12 col-sm-6 col-lg-2 m-3 d-flex align-items-center flex-column justify-content-between">
              <h5 class="card-title align-self-start fs-6">Number of Nurses</h5>
              <p class="card-text align-self-end fs-2">{{ hospital.overview.numberOfNurses }}</p>
            </div>
          </div> 
        
        
        <div class="custom-container my-4 mx-2">
        <div class="container">
          <h4 class="mb-3">Monthly hospitalizations</h4>
            <canvas :id="'hospitalChart-' + index"></canvas>
          </div>
      </div>

      <div class="container my-4">
      <div class="d-flex flex-column flex-md-row gap-3 justify-content-between">
        <div class="col-12 col-md-7 custom-container">
              <h4 class="mb-3">Clinical Trials</h4>
              <div class="btn-group" role="group" aria-label="Filter Trials">
                <button type="button" :class="{'btn button-primary': selectedStatus === 'All', 'btn btn-outline-secondary': selectedStatus !== 'All'}" @click="filterStatus('All')">All</button>
                <button type="button" :class="{'btn button-primary': selectedStatus === 'En cours', 'btn btn-outline-secondary': selectedStatus !== 'En cours'}" @click="filterStatus('En cours')">En cours</button>
                <button type="button" :class="{'btn button-primary': selectedStatus === 'Terminé', 'btn btn-outline-secondary': selectedStatus !== 'Terminé'}" @click="filterStatus('Terminé')">Terminé</button>
              </div>
              <table class="table table-striped">
                <thead>
                  <tr>
                    <th scope="col">Trial Name</th>
                    <th scope="col">Status</th>
                    <th scope="col">Start Date</th>
                    <th scope="col">End Date</th>
                    <th scope="col">Total Patients</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="(trial, trialIndex) in filteredTrials(hospital.clinicalTrials)" :key="'trial-' + trialIndex">
                    <td>{{ trial.name }}</td>
                    <td>{{ trial.status }}</td>
                    <td>{{ formatDate(trial.startDate) }}</td>
                    <td>{{ formatDate(trial.endDate) }}</td>
                    <td>{{ trial.totalPatients }}</td>
                  </tr>
                </tbody>
              </table>
            </div>

            <div class="col-12 col-md-5 custom-container">
                  <h4 class="mb-3">Hospital Departments</h4>
                  <canvas :id="'doughnutChart-' + index"></canvas>
            </div>  
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
import { Chart, BarController, BarElement, CategoryScale, LinearScale, Title, Tooltip, Legend, DoughnutController, ArcElement } from 'chart.js';

Chart.register(BarController, BarElement, CategoryScale, LinearScale, Title, Tooltip, Legend, DoughnutController, ArcElement);

export default {
    name:'hospitals',
  data() {
    return {
      hospitals: hospitalData,
      currentTab: 0 ,
      selectedStatus: 'All' 
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
    formatDate(dateString) {
      const options = { year: 'numeric', month: 'short', day: 'numeric' };
      return new Date(dateString).toLocaleDateString(undefined, options);
    },
    filteredTrials(clinicalTrials) {
      if (this.selectedStatus === 'All') {
        return clinicalTrials;
      }
      return clinicalTrials.filter(trial => trial.status === this.selectedStatus);
    },
    filterStatus(status) {
      this.selectedStatus = status;
    },
    renderChart(hospital, index) {
      if (this.chart) {
        this.chart.destroy();
      }
      if (this.doughnutChart) {
        this.doughnutChart.destroy();
      }
      
      const ctx = document.getElementById(`hospitalChart-${index}`).getContext('2d');
      const labels = hospital.monthlyHospitalizations.map(item => `${item.month} ${item.year}`);
      const data = hospital.monthlyHospitalizations.map(item => item.value);

      const gradient = ctx.createLinearGradient(0, 0, ctx.canvas.width, 0);
      gradient.addColorStop(0, '#CF55F4');
      gradient.addColorStop(1, '#7E3BFA');

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
      const doughnutCtx = document.getElementById(`doughnutChart-${index}`).getContext('2d');
      const doughnutLabels = hospital.hospitalDepartments.map(dept => dept.department);
      const doughnutData = hospital.hospitalDepartments.map(dept => dept.patientsPerDay);

      this.doughnutChart = new Chart(doughnutCtx, {
        type: 'doughnut',
        data: {
          labels: doughnutLabels,
          datasets: [{
            label: 'Patients Per Day',
            data: doughnutData,
            backgroundColor: ['#CF55F4', '#7E3BFA', '#B18CFE']
          }]
        },
        options: {
          responsive: true,
          plugins: {
            legend: {
              position: 'bottom',
            },
            title: {
              display: true,
              text: 'Patients Per Day by Department'
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

.table-striped>tbody>tr:nth-child(odd)>td, 
.table-striped>tbody>tr:nth-child(odd)>th {
  background-color: rgb(207, 85, 244, 0.5); 
 }

 .table-striped>tbody>tr:nth-child(even)>td, 
.table-striped>tbody>tr:nth-child(even)>th {
   background-color: rgb(126, 59, 250, 0.5);
 }

 .table-striped>thead>tr>th {
  background-color: var(--dark-blue);
  color: var(--text-color);
 }

.custom-card {
  background: var(--background-card);
  color: var(--text-color);
  box-shadow: rgba(0, 0, 0, 0.25) 0px 54px 55px, rgba(0, 0, 0, 0.12) 0px -12px 30px, rgba(0, 0, 0, 0.12) 0px 4px 6px, rgba(0, 0, 0, 0.17) 0px 12px 13px, rgba(0, 0, 0, 0.09) 0px -3px 5px;
}

.custom-container{
  background-color: var(--dark-blue);
  border-radius: 20px;
  padding: 15px;
  box-shadow: rgba(0, 0, 0, 0.19) 0px 10px 20px, rgba(0, 0, 0, 0.23) 0px 6px 6px;
  transition: transform 0.3s ease-in-out; 
}
.custom-container:hover {
  transform: scale(1.02); 
}

.hospital-overview {
  background-color: var(--dark-blue);
  height: 8rem;
  border-radius: 20px;
  box-shadow: rgba(0, 0, 0, 0.19) 0px 10px 20px, rgba(0, 0, 0, 0.23) 0px 6px 6px;
  transition: transform 0.3s ease-in-out; 
}
.hospital-overview:hover {
  transform: scale(1.03);
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

.button-primary {
  background: var(--gradient-color);
  color: var(--text-color);
}

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