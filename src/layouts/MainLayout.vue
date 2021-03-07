<template>
  <q-layout view="hHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="drawer = !drawer"
        />

        <q-toolbar-title>
          Thumbnail renderer
        </q-toolbar-title>
    <!-- <q-btn  v-if="!login" label="Login" @click="this.loginPortal" class="text-primary bg-white"></q-btn >
    <q-btn  v-if="login" label="Logout" @click="this.logoutPortal" class="text-primary bg-white"></q-btn > -->
        <div  v-if="login" >Create your thumbnails</div>
        <div class="q-px-xl">{{user}}</div>
      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view :drawer="drawer"/>
    </q-page-container>
  </q-layout>
</template>

<script  lang="ts" >

// import { loadModules } from 'esri-loader'
// import { loadModules } from "https://unpkg.com/esri-loader/dist/esm/esri-loader.js";

export default {
  name: 'MainLayout',
  data () {
    return {
      login: false,
      portal: 'https://maps.schiphol.nl/portal/',
      drawer: false,
      user: 'not logged in',
      esriId: null
    }
  },
  async mounted () {
    // @ts-ignore
    // type MapModules = [typeof import("esri/WebMap"), typeof import("esri/views/MapView"), typeof import('ersi/identity/IdentityManager'), typeof import('ersi/portal/Portal')];
    // @ts-ignore
    // const [esriConfig, OAuthInfo, esriId, Portal] = await ( loadModules(['ersi/config', 'esri/identity/OAuthInfo', 'ersi/identity/IdentityManager', 'ersi/portal/Portal']) as Promise<MapModules>);
    //     import esriConfig from 'ersi/config'
    // import OAuthInfo from
    // import esriId from
    // import Portal from
    // loadModules([
    //   'ersi/config',
    //   'esri/identity/OAuthInfo',
    //   'ersi/identity/IdentityManager',
    //   'ersi/portal/Portal'
    // ], {
    //   url: 'https://js.arcgis.com/4.4/'
    // })
    //   .then(([esriConfig, OAuthInfo, esriId, Portal]) => {
    // esriConfig.portalUrl = this.portal
    // this.esriId = esriId
    // var info = new OAuthInfo({
    //   appId: 'YJLdJZGwooKkrkPT',
    //   portalUrl: esriConfig.portalUrl,
    //   popup: false
    // })
    // esriId.registerOAuthInfos([info])
    // console.log(esriId.checkSignInStatus(esriConfig.portalUrl + 'sharing/rest'))
    // esriId.checkSignInStatus(esriConfig.portalUrl + 'sharing/rest').then(() => { this.mainMap() }).catch((e) => { console.log('NOT checkedin:', e) })
    //   }).catch(err => {
    //     // handle any script or module loading errors
    //     console.error(err)
    //   })
  },
  methods: {
    // mainMap () {
    //   loadModules([
    //     'ersi/portal/Portal'
    //   ], (Portal) => {
    //     var portal = new Portal()
    //     portal.authMode = 'immediate'
    //     console.log(123)
    //     portal.load().then(() => {
    //       console.log('Yess')
    //       this.login = true
    //       this.user = portal.user.username
    //     })
    //   })
    // },
    // loginPortal () {
    //   this.esriId.getCredential(this.portal + '/sharing')
    // },
    // logoutPortal () {
    //   this.esriId.destroyCredentials(); window.location.reload()
    // }
  }
}
</script>
