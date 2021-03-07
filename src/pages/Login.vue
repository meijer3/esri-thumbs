<template>

  <div class="fit row wrap justify-center items-start content-start" >
    <h1>Login</h1>
    <q-btn  v-if="login" label="Logout" @click="this.logoutPortal"></q-btn >
    <q-btn  v-if="!login" label="Login" @click="this.loginPortal"></q-btn >
  </div>
</template>

<script>
import esriConfig from 'arcgis-js-api/config'
// import Portal from 'arcgis-js-api/portal/Portal'
import OAuthInfo from 'arcgis-js-api/identity/OAuthInfo'
import esriId from 'arcgis-js-api/identity/IdentityManager'

export default {
  name: 'login',
  data () {
    return {
      login: false,
      portal: 'https://maps.schiphol.nl/portal/'
    }
  },
  mounted () {
    esriConfig.portalUrl = this.portal
    var info = new OAuthInfo({
      appId: 'YJLdJZGwooKkrkPT',
      portalUrl: esriConfig.portalUrl,
      popup: true
      // popupCallbackUrl: this.portal + '/?'
      // preserveUrlHash: true
    })
    esriId.registerOAuthInfos([info])
    esriId.checkSignInStatus(this.portal + 'sharing/rest').then(function () { console.log('Done') }).catch((e) => { console.log(e) })
    console.log(1)
  },
  methods: {
    loginPortal () {
      esriId.getCredential(this.portal + '/sharing')
    },
    logoutPortal () {
      esriId.destroyCredentials(); window.location.reload()
    }
  }
}
</script>
