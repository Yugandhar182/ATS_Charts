<script>
  import { Card } from 'flowbite-svelte';
  import { onMount,afterUpdate } from 'svelte';
  import { dateStore } from './DateStore.js'; // Import the store


  export let  appData;
   let metrics = null; 
   let startDate = "";
   let endDate = "";

   function getDatesFromLocalStorage() {
  const storedStartDate = localStorage.getItem('startDate');
  const storedEndDate = localStorage.getItem('endDate');

  if (storedStartDate && storedEndDate) {
    startDate = storedStartDate;
    endDate = storedEndDate;
    fetchData(startDate, endDate); // Fetch data when dates are loaded from local storage
  }
}

    // Subscribe to the dateStore
    dateStore.subscribe((value) => {
  startDate = value.startDate;
  endDate = value.endDate;
  fetchData(startDate, endDate); // Fetch data whenever the date changes
});


    async function fetchData(startDate, endDate) {
      try {
       
        const response = await fetch(`${appData.service.endpoint}/dashboard/ats/metrics?start=${startDate}&end=${endDate}&apiKey=${appData.service.apiKey}`);
  
        if (response.ok) {
          metrics = await response.json();
      } else {
          const errorText = await response.text();
          console.error("Failed to fetch data. Error:", errorText);
        }
      } catch (error) {
        console.error("Error:", error);
      }
    }
    onMount(() => {
      getDatesFromLocalStorage(); // Try to get dates from local storage
      fetchData(startDate, endDate); // Fetch data on component mount
    });
  
    afterUpdate(() => {
    
      localStorage.setItem('startDate', startDate);
      localStorage.setItem('endDate', endDate);
    });
 </script>

<div class="card-container">
 {#if metrics !== null}
   <div class="card1">
     <div class="Card">
       <span class="hover-text">Total Jobs</span>
       <h5   class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{metrics.totalJobs}</h5>
       <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Jobs</p>
     </div>
   </div>

   <div class="card2">
     <div class="Card">
       <span class="hover-text">Total Placements</span>
       <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{metrics.totalPlacements}</h5>
       <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Placements</p>
     </div>
   </div>

   <div class="card3">
     <div class="Card" style="background-color: rgb(84, 97, 243)">
       <span class="hover-text">Total Fee/Commission</span>
       <h5 style="color:white;" class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">GBP{metrics.totalCommission}</h5>
       <p style="color:white;" class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Billing</p>
     </div>
   </div>

   <div class="card4">
     <div class="Card">
       <span class="hover-text">Total Job Board Adverts</span>
       <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{metrics.totalAdverts}</h5>
       <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Adverts</p>
     </div>
   </div>

   <div class="card5">
     <div class="Card">
       <span class="hover-text">Avg.Time to Fill Days</span>
       <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{metrics.avgTimeToFillDays}</h5>
       <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight">TTF</p>
     </div>
   </div>

   <div class="card1">
     <div class="Card">
       <span class="hover-text">Avg.Candidates </span>
       <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{metrics.avgCandidatesPerPlacement}</h5>
       <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Candidates/Jobs</p>
     </div>
   </div>
 {/if}
</div>



<style>
 .card-container {
   display: flex;
   justify-content: space-between; /* Create space between cards */
   margin-left: 10px; /* Add margin around the container */
   margin-top: 100px;
   justify-content: center;
   flex-direction: row;
   border-radius: 0.25rem;
 }

 .Card {
   width: 200px;
   height: 100px;
   position: relative;
   margin: 40px; /* Add a margin to create a gap between cards */
   border: 1px solid rgba(20, 20, 31, 0.12);
   margin-right: 30px;
   font-weight: bold;
   font-size: 18px;
   display: flex;
   flex-direction: column;
   align-items: center; /* Center content horizontally */
   justify-content: center; /* Center content vertically */
   border-radius: 0.25rem;
   box-shadow: 0 0 0 1px rgba(20,20,31,.05), 0 1px 3px 0 rgba(20,20,31,.15);
 }

 
 .Card h5 {
   font-size: 24px; /* Adjust the font size for h5 */
 }

 .Card p {
   font-size: 16px; /* Adjust the font size for p */
   color: #888c9b;
   font-weight: 500;
   font-size: 18px;
   font-family: var(--tblr-font-sans-serif) ;
 
 }

 .Card:last-child {
   margin-right: 10px; /* Remove the right margin from the last card to avoid extra spacing */

 }
   .hover-text {
   display: none;
   position: absolute;
   background-color: rgba(0, 0, 0, 0.7);
   color: white;
   padding: 5px;
   border-radius: 5px;
   top: -50%;
   left: 0;
   right: 0;
   text-align: center; /* Center the text horizontally */
   font-size: 16px; /* Adjust the font size for hover text */
   z-index: 1;

 }

 .Card:hover .hover-text {
   display: block;
  
 }



</style>
