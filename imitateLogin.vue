<template>
<div class="container">
</div>
</template>

<script type="text/javascript">
import getQueryString from '../../../es/stringUtil';

export default {
  name: 'imitateLogin',
  data() {
    return {
      parmas: {
        username: '',
        password: '111111',
      },
    };
  },
  mounted() {
    if (getQueryString('userName')) {
      this.parmas.username = getQueryString('userName');
      this.checkCorpByMoblie();
    }
  },
  methods: {
    checkCorpByMoblie() {
      this.$http.post('checkCorpByMoblie.ajax', { account: this.parmas.username }).then(
        (res) => {
          res = res.body;

          if (res.rspData) {
            if (res.rspData.length > 1) {
              this.$message(res.rspMsg);
            } else if (res.rspData.length > 0) {
              this.parmas.corpid = res.rspData[0].id;
              this.parmas.account = `${this.parmas.corpid},${this.parmas.username}`;
              this.parmas.synSource = res.rspData[0].synSource;
            }
            this.loginAction();
          }
        },
        () => {
          this.$router.push('/');
        },
      );
    },
    loginAction() {
      const params = Object.assign({}, this.parmas, { channelcode: 'SSO' });
      this.$http.post('loginAction', params).then(
        () => {
          this.$router.push('/portal');
        },
        () => {
          this.$router.push('/');
        },
      );
    },
  },
};
</script>
