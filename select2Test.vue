<template>
<div class="container">
  <p>Selected: {{ selected }}</p>
  <select2 :options="options" v-model="selected">
    <option disabled value="0">Select one</option>
  </select2>
  <el-button @click.native="selected = 2">改变selected</el-button>
</div>
</template>


<script type="text/javascript">
import select2 from '../../modules/select2';

export default {
  name: 'select2Test',
  components: { select2 },
  data() {
    return {
      selected: 2,
      options: [
        { id: 1, text: 'Hello' },
        { id: 2, text: 'World' }
      ],
    };
  },
};
</script>
