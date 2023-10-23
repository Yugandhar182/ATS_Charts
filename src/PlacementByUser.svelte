<script>
    import { onMount, afterUpdate } from 'svelte';
    import { Chart, StackingColumnSeries, Category, DataLabel, Tooltip, Legend,Highlight } from '@syncfusion/ej2-charts';
    import { Browser } from '@syncfusion/ej2-base';
    import { dateStore } from './DateStore.js'; // Import the store
    Chart.Inject(StackingColumnSeries, Category, DataLabel, Tooltip, Legend, Highlight);

    export let appData;
    let chartData = []; // Initialize with an empty array
    let startDate = '';
    let endDate = '';
    let userNames = []; // Array to hold user names
    let noData = false;

    // Function to check if local storage has date information
    function getDatesFromLocalStorage() {
        const storedStartDate = localStorage.getItem('startDate');
        const storedEndDate = localStorage.getItem('endDate');

        if (storedStartDate && storedEndDate) {
            startDate = storedStartDate;
            endDate = storedEndDate;
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
            const response = await fetch(
                `${appData.service.endpoint}/dashboard/ats/data/placementmonthlyusermetrics?start=${startDate}&end=${endDate}&apiKey=${appData.service.apiKey}`
            );

           
            if (response.ok) {
                const jsonData = await response.json();

                // Create an array of unique month names dynamically
                const monthNames = [...new Set(jsonData.map(item => item.monthLabel))];

                // Dynamically extract user names from the data
                userNames = Object.keys(jsonData[0]).filter((key) => key !== 'monthLabel');

                // Transform API data into the format suitable for the chart
                chartData = monthNames.map((monthName) => {
                    const monthData = jsonData.filter((item) => item.monthLabel === monthName);

                    const transformedData = { monthLabel: monthName };
                    userNames.forEach((userName) => {
                        transformedData[userName] = monthData.reduce((total, item) => total + item[userName], 0) / 1000000;
                    });

                    return transformedData;
                });

                // After fetching data, update the chart
                updateChart();
            } else {
                console.error('Failed to fetch data from the API. HTTP status:', response.status);
            }
        } catch (error) {
            console.error('An error occurred while fetching data:', error);
        }
    }

    const colors = ['Tomato', 'DodgerBlue', 'Gold', 'LimeGreen', 'Purple', 'Orange', 'Crimson', 'RoyalBlue'];

    // Function to create or update the chart
    function updateChart() {
        let data = [];

chartData = chartData.map((val, index) => {
    var month = parseInt(val.monthLabel.split("/")[0]) - 1;
    var year = parseInt(val.monthLabel.split("/")[1]);
    val.monthLabel = new Date(year, month, 1);
    data.push(val);
    console.log("values", val);
});
        const chart = new Chart({
            primaryXAxis: {
                valueType: 'Category',
                majorGridLines: { width: 0 },
                labelStyle: {
                    size: '15px',
                    fontWeight: 'normal',
                },
            },
            primaryYAxis: {
                edgeLabelPlacement: 'Shift',
                majorTickLines: { width: 0 },
                minorTickLines: { width: 0 },
                lineStyle: { width: 0 },
                labelFormat: '{value}M',
                labelPlacement: 'OnTicks',
                minimum: 0,
                maximum: 5,
                interval: 1,
                title: 'Placement value',
                titleStyle: {
                    fontFamily: "'Segoe UI', 'Helvetica Neue', 'Trebuchet MS', Verdana, sans-serif",
                    fontWeight: 'Normal',
                    color: '#767676;',
                    size: '18px',
                },
                labelStyle: {
                    size: '15px',
                    fontWeight: 'normal',
                },
            },
            height: '350px',
            series: userNames.map((userName, index) => ({
                type: 'StackingColumn',
                dataSource: chartData,
                xName: 'monthLabel',
                width: 2,
                fill: colors[index],
                yName: userName,
                columnSpacing: 0.2,
                name: userName,
                columnWidth: 0.5,
            })),
            tooltip: {
                enable: true,
                format: 'GDP ${point.y}',
                fill: 'white',
                textStyle: {
                    color: 'black',
                    fontWeight: 'bold',
                },
                border: {
                    width: 4,
                    color: 'whitesmoke',
                },
            },
            legendSettings: { enableHighlight: true },
        });

        chart.appendTo('#container3');
    }

    onMount(() => {
        getDatesFromLocalStorage();
        fetchData(startDate, endDate);
    });

    afterUpdate(() => {
        fetchData(startDate, endDate);
        updateChart();
        localStorage.setItem('startDate', startDate);
        localStorage.setItem('endDate', endDate);
    });

</script>

<div class="card card-fluid">
    <div class="card-header border-0">
        Placement By User
    </div>
    <div class="card-body">
        <div id="container3"></div>
    </div>
</div>
<style>

.card {
    position: relative;
    display: flex;
    flex-direction: column;
    min-width: 0;
 
    background-color: #fff;
    background-clip: border-box;
    border: 1px solid rgba(20,20,31,.12);
    border-radius: 0.25rem;
    margin: 40px;
    top:5px;
    

}
   .card-body {
    flex: 1 1 auto;
    min-height: 1px;
    padding: 1rem;
}
.card-header {
  font-size: 16px; /* Adjust the font size as needed */
  font-weight: bold; /* Adjust the font weight as needed */
 

  padding: 15px; /* Adjust the padding as needed */
 
}
</style>
