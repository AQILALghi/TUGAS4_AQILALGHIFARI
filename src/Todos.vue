<template>
    <div class="container">
      <div class="header">
        <h1 class="title">Manajemen Kegiatan</h1>
      </div>
  
      <div class="add-activity">
        <h2>Tambah Kegiatan</h2>
        <input type="text" v-model="newActivity" placeholder="Nama Kegiatan">
        <input type="text" ref="datePicker" placeholder="Pilih Tanggal">
        <button @click="addActivity">Tambah</button>
      </div>
  
      <div class="filter">
        <h2>Filter Kegiatan</h2>
        <div class="filter-buttons">
          <button :class="{ 'active': filter === 'all' }" @click="setFilter('all')">Tampilkan Semua</button>
          <button :class="{ 'active': filter === 'incomplete' }" @click="setFilter('incomplete')">Tampilkan Belum Selesai</button>
        </div>
      </div>
  
      <div class="activity-list">
        <h2>Daftar Kegiatan</h2>
        <ul>
          <li v-for="(activity, index) in filteredActivities" :key="index" class="activity">
            <div class="nama">{{ activity.name }}</div>
            <div class="tanggal">{{ formatDate(activity.date) }}</div>
            <div class="actions">
              <button @click="removeActivity(index)">Hapus</button>
            </div>
            <div class="done">
              <input type="checkbox" v-model="activity.completed" @change="toggleActivity(activity)">
            </div>
          </li>
        </ul>
      </div>
    </div>
  </template>
  
  <script>
  import flatpickr from 'flatpickr';
  import 'flatpickr/dist/flatpickr.min.css';
  
  export default {
    data() {
      return {
        activities: [],
        newActivity: '',
        filter: 'all'
      };
    },
    computed: {
      filteredActivities() {
        if (this.filter === 'all') {
          return this.activities;
        } else if (this.filter === 'incomplete') {
          return this.activities.filter(activity => !activity.completed);
        }
      }
    },
    mounted() {
      flatpickr(this.$refs.datePicker, {
        dateFormat: 'Y-m-d',
        onChange: selectedDates => {
          this.selectedDate = selectedDates[0];
        }
      });
    },
    methods: {
      addActivity() {
        if (this.newActivity.trim() !== '') {
          this.activities.push({ name: this.newActivity, date: this.selectedDate, completed: false });
          this.newActivity = '';
        }
      },
      removeActivity(index) {
        this.activities.splice(index, 1);
      },
      toggleActivity(activity) {
        // Nothing needs to be done here, as v-model handles the update
      },
      setFilter(filter) {
        this.filter = filter;
      },
      formatDate(date) {
        const options = { day: '2-digit', month: '2-digit', year: 'numeric' };
        return new Date(date).toLocaleDateString('id-ID', options);
      }
    }
  }
  </script>
  
  <style scoped>
  
  body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    background-color: #f0f0f0;
  }
  
  /* Container */
  .container {
    background-color: #fff;
    border-radius: 20px;
    box-shadow: 
      20px 20px 60px rgba(0, 0, 0, 0.1),
      -20px -20px 60px rgba(255, 255, 255, 0.5);
    padding: 30px;
    display: flex;
    flex-direction: column;
    width: 800px; /* Adjust width as needed */
    margin: 0 auto; /* Menengahkan konten di halaman web desktop */
  }
  
  
  /* Header */
  .header {
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
  }
  
  .title {
    font-size: 24px;
    font-weight: bold;
    color: #333;
    text-align: center; /* Rata tengah judul */
    margin: 0;
  }
  
  /* Add Activity Component */
  .add-activity {
    display: flex;
    flex-direction: column; /* Change to column layout for better responsiveness */
    align-items: center;
    margin-bottom: 20px;
  }
  
  .add-activity input {
    flex: 1;
    width: 100%; /* Make input full width */
    margin-bottom: 10px; /* Add spacing between input and date picker */
    border: none;
    border-radius: 10px;
    padding: 10px;
    font-size: 16px;
    background-color: #f5f5f5;
    box-shadow: inset 2px 2px 4px rgba(0, 0, 0, 0.1),
               inset -2px -2px 4px rgba(255, 255, 255, 0.5);
  }
  
  .add-activity button {
    padding: 10px 20px;
    border: none;
    border-radius: 10px;
    font-weight: bold;
    cursor: pointer;
    background-color: #4CAF50; /* Green */
    color: white;
    box-shadow: 
      4px 4px 8px rgba(0, 0, 0, 0.2),
      -4px -4px 8px rgba(255, 255, 255, 0.7);
    transition: all 0.2s ease-in-out;
  }
  
  .add-activity button:hover {
    transform: scale(1.02);
  }
  
  /* Filter Activities Component */
  .filter-buttons {
    display: flex;
    justify-content: space-between;
    align-items: center; 
    margin-bottom: 20px;
    flex-direction: column;
    
  }
  
  .filter-buttons button {
    padding: 10px 20px;
    border: none;
    border-radius: 10px;
    font-weight: bold;
    cursor: pointer;
    background-color: #e0e0e0; /* Light gray */
    color: #333;
    box-shadow: 
      2px 2px 4px rgba(0, 0, 0, 0.1),
      -2px -2px 4px rgba(255, 255, 255, 0.7);
    transition: all 0.2s ease-in-out;
    width: 100%; /* Set the width to 100% for both mobile and desktop */
    margin-bottom: 10px; /* Adding margin-bottom for both mobile and desktop */
  }
  
  .filter-buttons button.active {
    background-color: #ccc; /* Darker gray */
    color: black;
  }
  
  .filter-buttons button:hover {
    transform: scale(1.02);
  }
  
  /* Activity List Component */
  .activity-list {
    flex: 1; /* Take up remaining space */
    display: flex;
    flex-direction: column;
    gap: 10px; /* Spacing between activities */
  }
  
  /* Individual Activity */
  .activity {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    align-items: center;
    padding: 10px;
    border-radius: 10px;
    background-color: #f9f9f9;
    box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1),
                -2px -2px 4px rgba(255, 255, 255, 0.7);
  }
  
  .activity.completed {
    background-color: #d9ead3; /* Light green */
  }
  
  .activity .nama,
  .activity .tanggal,
  .activity .actions {
    padding: 5px;
  }
  
  .activity .done {
    text-align: center;
  }
  
  .activity-name.completed {
    text-decoration: line-through;
    color: #999; /* Ubah warna teks untuk kegiatan yang selesai */
  }
  
  .activity-actions button {
    margin-left: 10px;
    padding: 5px 10px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    background-color: #ddd; /* Light gray */
    color: #333;
    box-shadow: 1px 1px 2px rgba(0, 0, 0, 0.1),
                -1px -1px 2px rgba(255, 255, 255, 0.7);
  }
  
  .activity-actions button:hover {
    background-color: #ccc; /* Darker gray */
  }
  
  
  /* Responsive Design */
  @media only screen and (max-width: 768px) {
    .container {
      width: 90%; /* Adjust width for smaller screens */
    }
  
    .add-activity input {
      font-size: 14px; /* Adjust font size for smaller screens */
    }
  
    .filter-buttons button {
      width: 100%; /* Set the width to 100% for smaller screens */
      margin-bottom: 10px; /* Adding margin to filter buttons for smaller screens */
    }
  }
  
  @media only screen and (max-width: 480px) {
    .add-activity input {
      font-size: 14px; /* Adjust font size for smaller screens */
    }
  
    .filter-buttons button {
      width: 100%; /* Set the width to 100% for even smaller screens */
      margin-bottom: 10px; /* Adding margin to filter buttons for even smaller screens */
    }
  }
  
  
  </style>
  