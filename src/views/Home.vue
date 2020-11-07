<template>
<div class="home">

    <Currency />

</div>
</template>

<script>
// @ is an alias to /src

import Currency from "@/components/Currency.vue";

export default {
    name: "Home",
    components: {

        Currency
    }
};
</script>
