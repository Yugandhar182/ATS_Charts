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
 {#if metrics !== null}
    <div class="card-container">
        <div class="card"> 
          <span class="hover-text">Total Jobs</span>
          <i class="fa fa-briefcase"style="margin-left:50px; margin-top:20px;" ></i><h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{metrics.totalJobs}</h5>
          <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Jobs</p>
          </div>

        <div class="card"> <span class="hover-text">Total Placements</span>
          <i class="fa fa-thumbs-o-up"  style="margin-left:50px; margin-top:20px; " ></i><h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{metrics.totalPlacements}</h5>
          <p  class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Placements</p>
        </div>

        <div class="card"  style="background-color:blue"> <span class="hover-text">Total Fee/Commission</span>
          <h5 style="color:white;margin-top:15px" class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">GBP{metrics.totalCommission}</h5>
          <p style="color:white;" class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Billing</p>
        </div>

        <div class="card"><span class="hover-text">Total Job Board Adverts</span>
          <i class="fa fa-bullhorn"style="margin-left:55px; margin-top:20px;" ></i> <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{metrics.totalAdverts}</h5>
          <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Adverts</p>
        </div>

        <div class="card"><span class="hover-text">Avg.Time to Fill Days</span>
          <i class="fa fa-clock-o"style="margin-left:55px; margin-top:20px;" ></i> <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{metrics.avgTimeToFillDays}</h5>
          <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight">TTF</p>
        </div>

        <div class="card"> <span class="hover-text">Avg.Candidates </span>
          <i class="fa fa-user"style="margin-left:60px; margin-top:20px;" ></i>  <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{metrics.avgCandidatesPerPlacement}</h5>
          <p class="font-normal text-gray-700 dark:text-gray-400 leading-tight">Candidates/Jobs</p></div>
    </div>
    {/if}

<style>.card-container {
  display: flex;
  margin: 0 32px; 

}
.card {
  position: relative;
  width: calc(100vw / 6);
  height: 120px;
  background-color:white;
  margin: 10px;
  padding: 20px;
  box-sizing: border-box;
 margin-top: 120px;
}

.card h5 {
  font-size: 24px; /* Adjust the font size for h5 */

  text-align: center;
  margin-top: -30px;
}
.fa-lg {
    font-size: 1.25em;
    line-height: .05em;
    vertical-align: -0.075em;
}


.card p {
  font-size: 16px; /* Adjust the font size for p */
  color: #888c9b;

  font-weight: 500;
  font-size: 18px;
  font-family: var(--tblr-font-sans-serif) ;
  text-align: center;

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

.card:hover .hover-text {
  display: block;
 
}


</style>
