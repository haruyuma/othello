<template>
  <span v-html="content" style="font-size:30px"></span>
</template>  
  
<script>
export default {
    props: ['type'],
    computed: {
        content() {
            if(this.type == '-1') { // 黒
                return '&#x26AB;';
            } else if(this.type == '1') { // 白
                return '&#x26AA;';
            }
                return '&nbsp;';
        }
    },
}
</script>