<template>

    <div class="flex_col">
        <h2>{{$t('calculator.results.title')}}</h2>
        <v-expansion-panels popout focusable multiple>
            <SchockWaveResults/>
            <Irradiation/>
            <Crater/>
            <Ejecta/>
            <Seismic/>
            <AtmosphericDisturbances/>
        </v-expansion-panels>
    </div>

</template>

<script lang="ts">
import { Component, Prop, Vue, Model, ModelSync } from 'vue-property-decorator';
import State from '../../../model/State';
import SchockWaveResults from './SchockWave.vue';
import Irradiation from './Irradiation.vue';
import Crater from './Crater.vue';
import Ejecta from './Ejecta.vue';
import Seismic from './Seismic.vue';
import AtmosphericDisturbances from './AtmosphericDisturbances.vue';


@Component({
    components: {
        SchockWaveResults,
        Irradiation,
        Crater,
        Ejecta,
        Seismic,
        AtmosphericDisturbances
    }
})
export default class Results extends Vue {
    state: State = State.state;
}
</script>

<style scoped lang="scss">

</style>