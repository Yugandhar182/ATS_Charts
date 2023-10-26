<script>
    export let appData; // Export appData from main.svelte
    import Calendar from './DateRangePicker.svelte';
    import Metric from './Metric.svelte';
    import Time from "./TimeToFill.svelte"
    import Placement from "./PlacementByUser.svelte"
    import Reject from "./RejectedReasons.svelte"

</script>
  
 <body>
    <Calendar/>
    <Metric {appData}/>
    <Time {appData}/>
    <Placement {appData}/>
    <Reject {appData}/>
</body>
 
    <style>
            body{
                background-color: #f6f7f9;
               height: 1700px;
                margin: 0px;
                margin-top:-40px
             }
 
    </style>
