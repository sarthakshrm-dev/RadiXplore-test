<template>
    <div>
      <button class="form-show" v-show="isSmallScreen" @click="() => {isVisible = true}">Marker</button>
      <div v-show="isVisible" class="marker-form-container">
        <button v-show="isSmallScreen" class="close-form" @click="() => {isVisible = false}">X</button>
        <h2>Add Marker</h2>
        <form @submit.prevent="submitForm">
          <div class="form-group">
            <label for="name">Name:</label>
            <input type="text" id="name" v-model.trim="formData.name" required>
          </div>
          <div class="form-group">
            <label for="description">Description:</label>
            <textarea id="description" v-model.trim="formData.description" required></textarea>
          </div>
          <div class="form-group">
            <label for="latitude">Latitude:</label>
            <input type="number" id="latitude" v-model.trim="formData.latitude" @input="restrictDecimalLength($event, 2)" required step="0.01">
          </div>
          <div class="form-group">
            <label for="longitude">Longitude:</label>
            <input type="number" id="longitude" v-model.trim="formData.longitude" @input="restrictDecimalLength($event, 2)" required step="0.01">
          </div>
          <div class="form-group">
            <button type="submit" :disabled="!isFormValid">Submit</button>
          </div>
        </form>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'MarkerForm',
    data() {
      return {
        formData: {
          name: '',
          description: '',
          latitude: '',
          longitude: ''
        },
        isVisible: true,
        isSmallScreen: false
      };
    },
    computed: {
      isFormValid() {
        const { name, description, latitude, longitude } = this.formData;
        return name && description && latitude && longitude;
      }
    },
    methods: {
      submitForm() {
        const { latitude, longitude } = this.formData;
        const roundedLatitude = parseFloat(latitude).toFixed(2);
        const roundedLongitude = parseFloat(longitude).toFixed(2);
  
        this.$emit('form-submit', {
          ...this.formData,
          latitude: roundedLatitude,
          longitude: roundedLongitude
        });
  
        this.resetForm();
      },
      resetForm() {
        if (this.isSmallScreen) {
          this.isVisible = false;
        }
        this.formData = {
          name: '',
          description: '',
          latitude: '',
          longitude: ''
        };
      },
      restrictDecimalLength(event, decimalPlaces) {
        const { value, id } = event.target;
        const regex = new RegExp(`^[0-9]+(\\.[0-9]{0,${decimalPlaces}})?$`);
        if (!regex.test(value)) {
          event.target.value = value.slice(0, value.lastIndexOf('.') + decimalPlaces + 1);
        }
        this.formData[id] = event.target.value;
      },
      updateVisibility() {
        const screenWidth = window.innerWidth;
        this.isVisible = screenWidth >= 750;
        this.isSmallScreen = screenWidth <= 750;
      }
    },
    mounted() {
      window.addEventListener('resize', this.updateVisibility);
      this.updateVisibility();
    },
    beforeDestroy() {
      window.removeEventListener('resize', this.updateVisibility);
    }
  };
  </script>
  
  <style scoped>
  .form-show {
    position: absolute;
    top: 20px;
    right: 20px;
    background-color: #fff;
    padding: 10px;
    border-radius: 10px;
    z-index: 1000;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
    border: none;
  }

  .close-form {
    position: absolute;
    top: 10px;
    right: 10px;
    background-color: transparent;
    border: none;
  }
  
  .marker-form-container {
    position: absolute;
    top: 20px;
    right: 20px;
    background-color: #fff;
    padding: 20px;
    border-radius: 10px;
    z-index: 1000;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
  }
  
  .marker-form-container h2 {
    font-size: 20px;
    font-weight: bold;
    margin-bottom: 20px;
  }
  
  .marker-form-container form {
    display: grid;
    grid-gap: 10px;
  }
  
  .marker-form-container .form-group {
    display: grid;
    grid-template-columns: max-content 1fr;
    align-items: center;
  }
  
  .marker-form-container label {
    font-weight: bold;
    text-align: right;
  }
  
  .marker-form-container input[type="text"],
  .marker-form-container input[type="number"],
  .marker-form-container textarea {
    padding: 5px;
    border: 1px solid #ccc;
    border-radius: 4px;
    width: 100%;
    font-size: 14px;
  }
  
  .marker-form-container button[type="submit"] {
    background-color: #4caf50;
    color: #fff;
    border: none;
    font-size: 14px;
    border-radius: 4px;
    cursor: pointer;
    transition: background-color 0.3s;
    padding: 5px;
  }
  
  .marker-form-container button:disabled {
    background-color: #ccc;
    cursor: not-allowed;
  }
  
  input::-webkit-outer-spin-button,
  input::-webkit-inner-spin-button {
    -webkit-appearance: none;
    appearance: none;
    margin: 0;
  }
  
  input[type=number] {
    -moz-appearance: textfield;
    appearance: textfield;
  }
  </style>