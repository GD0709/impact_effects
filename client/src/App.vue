<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <router-link class="header_link" to="/">
        <div class="d-flex align-center">
          <span class="icon ah-meteoroid logo"/>
        <div class="app_bar_header">Impact Effects{{visual.is_debug ? ' - Debug Mode' : ''}}</div>
        </div>
      </router-link>

      <v-spacer></v-spacer>

      <!-- <v-btn class="d-none d-sm-flex"
        href="http://isturunt.pythonanywhere.com/"
        target="_blank"
        text
      >
        <span class="mr-2">{{ $t("old version") }}</span>
        <v-icon>mdi-open-in-new</v-icon>
      </v-btn> -->
      <v-btn class="d-none d-sm-flex"
        text
        v-on:click="test"
      >
        <span class="mr-2">test</span>
        <v-icon>mdi-open-in-new</v-icon>
      </v-btn>
    </v-app-bar>

    <v-main>
      <router-view/>
    </v-main>
        <v-footer
      :padless="true"
    >
      <v-card
        flat
        tile
        width="100%"
        color="primary" dark
        class="text-center"
      >
        <v-card-text>
          
          <div class="flex_row_center_top">
            <div class="flex_col_top_left footer_column">
              <div class="footer_caption white--text" >{{ $t("footer.navigation") }}</div>
              <router-link class="white--text" to="/">{{ $t("footer.about") }}</router-link>
              <router-link class="white--text" to="/c">{{ $t("footer.calculator") }}</router-link>
              <router-link class="white--text" to="/articles">{{ $t("footer.articles") }}</router-link>
              <a href="http://isturunt.pythonanywhere.com/"
                target="_blank"
                class="white--text">{{ $t("old version") }}</a>
            </div>

            <div class="flex_col_top_left footer_column">
              <div class="footer_caption white--text" >Language</div>
              <button v-on:click="locale='ru'">Русский</button>
              <button v-on:click="locale='en'">English</button>
            </div>

          </div>
          



        </v-card-text>

        <v-divider></v-divider>

        <v-card-text class="white--text">
          <DebugSwitcher>{{ new Date().getFullYear() }} — {{$t("footer.footer_text")}}</DebugSwitcher>
          <div><a class="footer_link" href="https://idg.ras.ru/">{{$t("footer.organization")}}</a></div>
        </v-card-text>
      </v-card>
    </v-footer>
  </v-app>
</template>

<script lang="ts">
import Vue from 'vue';
import { Component } from 'vue-property-decorator';
import DebugSwitcher from './components/DebugSwitcher.vue'
import State from './components/model/State';
import {VisualSettings} from './components/model/VisualSettings';
import LatLon from 'geodesy/latlon-ellipsoidal-vincenty.js';


@Component({
    components: {
        DebugSwitcher
    }
})
export default class App extends Vue {    
  visual: VisualSettings = State.state.visual_settings;
    created()
    {
      //console.log(this.$t);
    }
    get locale(): string {return this.$i18n.locale; }
    set locale(val: string) {this.$i18n.locale = val;}


    test() {
      const p1 = new LatLon(-37.95103, 144.42487);
const dist = 54972.271;
const brng = 306.86816;
const p2 = p1.destinationPoint(dist, brng);
let dist2 = p1.distanceTo(p2);

      console.log("hello 2", p2, " and distance is ", dist2);
    }
}

</script>

<style scoped lang="scss">
  .header_link{
    text-decoration: none;
    color:white;
  }
  .app_bar_header{
    font-weight: 400;
    font-size: 28px;
  }
  .logo{
    color:white;
    font-size: 36px;
    margin-top: -10px;
    margin-right: 10px;
  }
  .footer_column{
    margin-right:50px; 
    margin-left:50px; 
  }
  .footer_caption{
    font-size: 18px;
    margin-bottom: 16px;
  }
  footer a {
    color:white;
    text-decoration: unset;
    &:hover{
      text-decoration: underline;
    }
  }
</style>