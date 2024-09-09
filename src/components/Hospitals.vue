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
          <h2 class="card-header">
            {{ hospital.name }}
          </h2>
          <div class="card-body">
              <div class="d-flex align-items-center card-title">      
                  <span class="material-symbols-outlined fs-6 me-1">
                    location_on
                    </span>
                    <div>{{ hospital.location }}</div>
                </div>

            <p class="card-text">Total Patients: {{ hospital.overview.totalPatients }}</p>
            <p class="card-text">Satisfaction Rate: {{ hospital.overview.satisfactionRate }}</p>
          </div>
          <div class="container my-4">
          <div class="row">
            <div class="col-12 col-md-6 col-lg-4 mb-3">
              <div class="card p-3">
                <h5 class="card-title">Total Patients</h5>
                <p class="card-text">{{ hospital.overview.totalPatients }}</p>
              </div>
            </div>
            <div class="col-12 col-md-6 col-lg-4 mb-3">
              <div class="card p-3">
                <h5 class="card-title">Satisfaction Rate</h5>
                <p class="card-text">{{ hospital.overview.satisfactionRate }}</p>
              </div>
            </div>
            <div class="col-12 col-md-6 col-lg-4 mb-3">
              <div class="card p-3">
                <h5 class="card-title">Total Treatments</h5>
                <p class="card-text">{{ hospital.overview.totalTreatments }}</p>
              </div>
            </div>
            <div class="col-12 col-md-6 col-lg-4 mb-3">
              <div class="card p-3">
                <h5 class="card-title">Number of Doctors</h5>
                <p class="card-text">{{ hospital.overview.numberOfDoctors }}</p>
              </div>
            </div>
            <div class="col-12 col-md-6 col-lg-4 mb-3">
              <div class="card p-3">
                <h5 class="card-title">Number of Nurses</h5>
                <p class="card-text">{{ hospital.overview.numberOfNurses }}</p>
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

export default {
    name:'hospitals',
  data() {
    return {
      hospitals: hospitalData,
      currentTab: 0 
    };
  }
}
</script>

<style scoped>
.custom-card {
  background: var(--background-card);
  color: var(--text-color);
}

.card-title {
  color: #B18CFE;
}

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