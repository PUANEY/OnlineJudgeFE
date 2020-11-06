<template>
  <div>
    <NavBar></NavBar>
    <div class="content-app">
      <transition name="fade-transform" mode="out-in">
        <router-view></router-view>
      </transition>
      <div class="footer">
        {{ says.hitokoto }}
        <div class="slogan">
          Powerby <a href="http://www.ahujhc.cn/">安徽大学江淮学院创新实验室</a>
        </div>
      </div>
    </div>
    <BackTop></BackTop>
  </div>
</template>

<script>
import { mapActions, mapState } from "vuex";
import NavBar from "@oj/components/NavBar.vue";

export default {
  name: "app",
  components: {
    NavBar,
  },
  data() {
    return {
      version: process.env.VERSION,
      says: {},
    };
  },
  created() {
    try {
      document.body.removeChild(document.getElementById("app-loader"));
    } catch (e) {}
  },
  mounted() {
    this.getWebsiteConfig();
    this.$http("http://v1.hitokoto.cn", {
      params: {
        c: "k",
      },
    }).then((res) => {
      this.says = res.data;
    });
  },
  methods: {
    ...mapActions(["getWebsiteConfig", "changeDomTitle"]),
  },
  computed: {
    ...mapState(["website"]),
  },
  watch: {
    website() {
      this.changeDomTitle();
    },
    $route() {
      this.changeDomTitle();
    },
  },
};
</script>

<style lang="less">
* {
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
}

a {
  text-decoration: none;
  background-color: transparent;
  &:active,
  &:hover {
    outline-width: 0;
  }
}

@media screen and (max-width: 1200px) {
  .content-app {
    margin-top: 160px;
    padding: 0 2%;
  }
}

@media screen and (min-width: 1200px) {
  .content-app {
    margin-top: 80px;
    padding: 0 2%;
  }
}

.footer {
  margin-top: 20px;
  margin-bottom: 10px;
  text-align: center;
  font-size: small;
}
.slogan {
  margin-top: 10px;
}

.fade-transform-leave-active,
.fade-transform-enter-active {
  transition: all 0.5s;
}

.fade-transform-enter {
  opacity: 0;
  transform: translateY(-30px);
}

.fade-transform-leave-to {
  opacity: 0;
  transform: translateY(30px);
}
</style>
