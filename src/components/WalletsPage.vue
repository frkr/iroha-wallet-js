<template>
  <div class="wallets-page" v-loading="!isReady">
    <div class="sidemenu">
      <router-link
        v-for="wallet in wallets"
        :key="wallet.id"
        class="sidemenu__item"
        :to="'/dashboard/wallets-page/' + wallet.id"
      >
        <div>{{ wallet.name }}</div>
        <div>{{ wallet.amount }}</div>
      </router-link>
    </div>

    <div class="main">
      <router-view />

      <div v-if="wallets.length === 0">No assets</div>
    </div>
  </div>
</template>

<script>
  import { mapGetters } from 'vuex'

  export default {
    name: 'wallets-page',

    data () {
      return {
        isReady: false
      }
    },

    computed: {
      ...mapGetters({
        wallets: 'wallets'
      })
    },

    watch: {
      '$route' (to) {
        // If moved from 'wallet' to 'wallets-page' (i.e. no wallet opens),
        // open the default one.
        if (to.name === 'wallets-page') {
          this.openDefaultWallet()
        }
      }
    },

    mounted () {
      // If moved from other pages to 'wallets-page', open the default one.
      this.openDefaultWallet()
    },

    created () {
      this.$store.dispatch('getAllAccountAssetsTransactions')
        .finally(() => { this.isReady = true })
    },

    methods: {
      openDefaultWallet () {
        if (this.wallets[0]) {
          this.$router.push({ name: 'wallet', params: { walletId: this.wallets[0].id } })
        }
      }
    }
  }
</script>

<style lang="scss" scoped>
  @import "~@/styles/element-variables.scss";

  .wallets-page {
    display: flex;
  }

  .sidemenu {
    $sidemenu-width: 150px;

    height: 100vh;
    overflow: auto;

    &__item {
      display: block;
      width: $sidemenu-width;
      padding: 1rem;
      text-decoration: none;
      color: #7e7e7e;
      background: #9d9d9d;
      border: 1px solid #aaa;
      border-right: 1px solid white;
      transition: .1s ease background;

      &:hover {
        background: darken(#9d9d9d, 5%);
      }
    }

    .router-link-active {
      background: white;
      color: inherit;
    }
  }

  .main {
    flex: 1;
    height: 100vh;
    background: white;
    overflow: auto;
  }
</style>
