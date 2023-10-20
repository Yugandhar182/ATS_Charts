<script>


    export let appData; // Export appData from main.svelte
    import Calendar from './DateRangePicker.svelte';
    import Metric from './Metric.svelte';
    import Time from "./TimeToFill.svelte"
    import Placement from "./PlacementByUser.svelte"
    import Reject from "./RejectedReasons.svelte"
   </script>
  
 
    <Calendar/>
    <Metric {appData}/>
    <Time {appData}/>
    <Placement {appData}/>
    <Reject {appData}/>
    
 
    <style>
      
    </style>