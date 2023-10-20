<script>
  import { onMount, afterUpdate } from 'svelte';
  import { DateRangePicker } from '@syncfusion/ej2-calendars';
  import { dateStore } from './DateStore.js'; // Import the store
 
  let daterangepicker;
 
  let startDate = localStorage.getItem('startDate') || '';
  let endDate = localStorage.getItem('endDate') || '';

  onMount(() => {
    initializeDateRangePicker(startDate, endDate);
  });

  afterUpdate(() => {
    // After the component updates, check if the local storage values have changed
    const updatedStartDate = localStorage.getItem('startDate') || '';
    const updatedEndDate = localStorage.getItem('endDate') || '';

    if (startDate !== updatedStartDate || endDate !== updatedEndDate) {
      startDate = updatedStartDate;
      endDate = updatedEndDate;
    }
  });

  function initializeDateRangePicker(initialStartDate, initialEndDate) {
    daterangepicker = new DateRangePicker({
      placeholder: 'Select Range',
      start: 'Year', 
      depth: 'Year', 
      format: 'MMM yyyy',
      value: [
        new Date(initialStartDate || new Date()), // Initialize with either stored date or a default date
        new Date(initialEndDate || new Date())   // Initialize with either stored date or a default date
      ],
      change: () => {
        const selectedDates = daterangepicker.getSelectedRange();
        if (selectedDates && selectedDates.startDate && selectedDates.endDate) {
          const selectedStartDate = formatSelectedDate(selectedDates.startDate);
          const selectedEndDate = formatSelectedDate(selectedDates.endDate);
          localStorage.setItem('startDate', selectedStartDate);
          localStorage.setItem('endDate', selectedEndDate);
          dateStore.set({ startDate: selectedStartDate, endDate: selectedEndDate });
         
        }
      }
    });
    daterangepicker.appendTo('#element');
;
  }

  function formatSelectedDate(date) {
    const day = date.getDate();
    const month = date.getMonth() + 1;
    const year = date.getFullYear();
    return `${day.toString().padStart(2, '0')}/${month.toString().padStart(2, '0')}/${year}`;
  }


</script>
<div class="calendar-container">
  <div class="col-lg-12 control-section">
    <div id="wrapper">
      <input id="element" type="text" /><br/><br/>
    </div>
  </div>
</div>
<style>

.calendar-container {
    position: absolute;
    top: 30px; /* Adjust the top position as needed */
    right: 15px; /* Adjust the left position as needed */
    width: 300px;
    height: 60px;
    text-align: center;
    align-items: center; /* Center content horizontally */

  }
#element{
    width :210px;
    height: 40px;
    border-color: #ced4da !important;
    background: linear-gradient(180deg,#fff,#f6f7f9) !important;
    transition: box-shadow .2s ease-in-out,border-color .2s ease-in-out !important;
    box-shadow: inset 0 1px 0 0 rgba(34,34,48,.05);
    border: 1px solid;
    border-width: 1px;
    margin: 10PX;
    position: relative; 
    font-size: 16px;
    font-weight: 400px;
    font-family: Arial, sans-serif;
    color: #333;
    top:0px;
    border-radius: 0.25rem;
 
  }


</style>