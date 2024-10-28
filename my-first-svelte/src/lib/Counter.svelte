<script>
    let count = 0 ;
    function incrementCount(){
        count++;
    }

</script>
<p>
    The number of Count is : {count}
</p>
<button on:click={incrementCount}> +1 </button>