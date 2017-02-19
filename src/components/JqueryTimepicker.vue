<template>
  <input />
</template>
<script>
  /* eslint-disable */

  import 'expose-loader?$!expose-loader?jQuery!jquery'
  import 'timepicker/jquery.timepicker.min.css'
  import 'timepicker/jquery.timepicker.min.js'

  export default {
    props: ['options', 'value'],
    mounted () {

      let thisVM = this
      $(this.$el).timepicker({
        'scrollDefault': 'now',
        'timeFormat': 'H:i'
      })
      $(this.$el).on('changeTime', function () {
        //console.log('JQUERY TIMEPICKER : ', typeof this.value)
        //console.log('TIME : ', $(this).timepicker('getTime'))

        thisVM.$emit('input', $(this).timepicker('getTime'))
        //$('#onselectTarget').text($(this).val());
      })
    },
    watch: {
      value: function (value) {
        // update value
        $(this.$el).timepicker('setTime', value)
      },
      options: function (options) {
        // update options
        $(this.$el).timepicker(options)
      }
    },
    destroyed: function () {
      $(this.$el).timepicker('remove')
    }
  }

</script>
<style>


</style>
