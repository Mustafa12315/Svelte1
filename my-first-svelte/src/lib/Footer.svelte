<script>
    export let year = 2024;
    export let name = 'bird';
    export let thisismypropname;
    let date = new Date();
</script>

<div>
    <img src={thisismypropname} alt={name}> 
<p>&copy; Web 1 {year} : {date}</p>
</div>
<style>
    p{
        color: chartreuse;
        padding: 2em;
        font-weight: bold;
        font-size: large;
        border: 2px dotted dodgerblue;
    }
</style>