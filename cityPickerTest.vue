<template>
<div class="container">
  <citypicker></citypicker>
</div>
</template>

<script type="text/javascript">
import citypicker from '../../modules/citypicker';

export default {
  name: 'cityPickerTest',
  components: { citypicker },
  data() {
    return {
      selected: 2,
    };
  },
};
</script>
