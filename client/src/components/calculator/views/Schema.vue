<template>
    <v-col>
        <svg viewBox="0 0 234 136"  preserveAspectRatio="xMidYMid meet"  overflow="visible" ref="svg">
 <!--            <rect width="234" height="136" x="0" y="0"
                fill="yellowgreen"
                stroke="yellowgreen"/> -->
            <symbol v-for="(marker, name) in markers" 
                :key=name 
                :id="'icon_' + name"
                preserveAspectRatio="xMidYMid meet" viewBox="16 16 32 32" overflow="visible">
                <g class="marker_figure">
                    <circle cx="16" cy="16" r="16" style="fill: var(--marker_back, #d3d3d32d); stroke: var(--marker_stroke, black);"></circle> 
                    <text x="16" y="16" dy="0.33em" text-anchor="middle" class="icon"  stroke="none" fill="#ededed"
                        style="fill: var(--icon_color, black);" v-html="marker.icon.code">
                    </text>
                </g>
            </symbol>

                <svg x="0" y="0" viewBox="-10 -126 220 130" width="220" height="130" preserveAspectRatio="xMidYMid meet"  overflow="visible">
                     <svg x="0" y="0" viewBox="0 0 210 10" width="210" height="10" preserveAspectRatio="xMidYMid meet"  overflow="hidden">
                        <!-- earth surface -->
                        <circle :class="{1600: 'sand', 2650: 'rock'}[state.target.target_density]"
                            style="stroke-width: 6px; stroke-opacity: 0.5; fill: none;" cx="0" cy="6400" r="6397"></circle>
 <!--                        <line style="stroke: red; " x1="0" y1="10" :x2="0" :y2="-10"></line>
                        <line style="stroke: red; " x1="50" y1="10" :x2="50" :y2="-10"></line> -->
                     </svg>

                <!-- trajectory -->
                <line style="stroke: rgb(175, 175, 175); stroke-dasharray: 4px;" x1="0" y1="0" :x2="airburst_point.x" :y2="-airburst_point.x * Math.tan(state.variant.angle_rad)"></line>
                <line style="stroke: rgb(175, 175, 175);" :x1="airburst_point.x" :y1="-airburst_point.x * Math.tan(state.variant.angle_rad)" :x2="false_entry.x" :y2="-false_entry.y"></line>
                
            

                <text text-anchor="middle" alignment-baseline="middle"
                    v-if="state.variant.angle_rad < Math.atan( (100)/(200))"
                    class="entry_vactor_arrow"
                    :x="arrow_placement.x" 
                    :y="arrow_placement.y"  
                    :transform="'rotate('+ (-state.variant.angle-90)+' '+ arrow_placement.x + ' ' + arrow_placement.y + ')'"
                        >↑
                </text>
<!--                 <text x="105" :y="-80" dy="0.13em" text-anchor="middle" stroke="none"
                    alignment-baseline="end"
                    :transform="'rotate('+ (-state.variant.angle-90)+' '+ arrow_placement.x + ' ' + (arrow_placement.map_cs.y) + ')'"
                    class="axe_label_text">
                    
                    {{'rotate('+ (-state.variant.angle-90)+' '+ arrow_placement.x + ' ' + arrow_placement.y + ')'}}
                </text> -->

                <!-- entry -->
                <g class="effects_group hover_shadow_back" @mouseover="markers.entry.icon.tooltip = true" @mouseout="markers.entry.icon.tooltip = false">
                    <use class="use_marker meteoroid" 
                        height="16" width="16" xlink:href="#icon_entry" 
                        :x="markers.entry.map_cs.x" 
                        :y="-markers.entry.map_cs.y" 
                        :transform="'rotate('+ (-state.variant.angle+55)+' '+ markers.entry.map_cs.x + ' ' + (-markers.entry.map_cs.y) + ')'"></use>
                </g>

                <!-- intersection -->
                <g class="effects_group hover_shadow_back" @mouseover="markers.intersection.icon.tooltip = true" @mouseout="markers.intersection.icon.tooltip = false">
                    <use class="use_marker" :x="markers.intersection.map_cs.x" :y="- markers.intersection.map_cs.y" height="16" width="16" :xlink:href="'#icon_' + 'intersection'"></use> 
                </g>

                <!-- radiation -->
                <g class="effects_group hover_shadow_back" @mouseover="markers.irradiation.icon.tooltip = true" @mouseout="markers.irradiation.icon.tooltip = false">
  <!--                   <line class="sizes_line" :x1="irradiation_point.x" y1="0" :x2="irradiation_point.x" :y2="- top_axe_origin_y"></line>    --> 
                    <use class="use_marker" :x="markers.irradiation.map_cs.x" :y="- markers.irradiation.map_cs.y" height="16" width="16" xlink:href="#icon_irradiation"></use> 
                </g>


                <!-- shock wave -->
                <g class="effects_group hover_shadow_back" @mouseover="markers.overpressure.icon.tooltip = true" @mouseout="markers.overpressure.icon.tooltip = false">
                    <line class="sizes_line" :x1="markers.overpressure.map_cs.x" y1="0" :x2="markers.overpressure.map_cs.x" :y2="- top_axe_origin_y"></line>
                    <line class="sizes_line" :x1="0" :y1="-markers.overpressure.map_cs.y" :x2="200" :y2="- markers.overpressure.map_cs.y"></line>
                    <use class="use_marker" :x="markers.overpressure.map_cs.x" :y="- markers.overpressure.map_cs.y" height="16" width="16" xlink:href="#icon_overpressure"></use>
                </g>
                










                <!-- frame -->
                <path stroke="black" fill="none" stroke-width="0.5" stroke-opacity="0.5"
                    d="M0 0 L210 0 L210 -110 L0 -110 L0 0"/>
                <!-- frame ticks bottom -->
                <path stroke="black" fill="none" stroke-width="0.5" stroke-opacity="0.5"
                    v-for="xl in x_ticks" :key="'axetick-bottom-x' + xl.pos"
                    :d="'M' + xl.pos  + ' -3 L' + xl.pos + ' 0'"/>
                <!-- frame ticks top -->
                <path stroke="black" fill="none" stroke-width="0.5" stroke-opacity="0.5"
                    v-for="xl in x_ticks" :key="'axetick-top-x' + xl.pos"
                    :d="'M' + xl.pos  + ' ' + (0-top_axe_origin_y) + ' L' + xl.pos + ' ' + (3-top_axe_origin_y)"/>

                <!-- x-ticks labels -->
                <text :x="xl.pos" y="6" dy="0.13em" text-anchor="middle" stroke="none"
                    alignment-baseline="hanging"
                    class="axe_tick_text"
                    v-for="xl in x_ticks" :key="'axetick-bottom-text-x' + xl.pos">
                            {{xl.label}}
                </text>
                <text :x="xl.pos" :y="-top_axe_origin_y-4" dy="0.13em" text-anchor="middle" stroke="none"
                    alignment-baseline="baseline"
                    class="axe_tick_text"
                    v-for="xl in x_ticks" :key="'axetick-top-text-x' + xl.pos">
                            {{xl.label}}
                </text>


                <!-- y axe label -->
                <text x="226" :y="-50" dy="0.13em" text-anchor="middle" stroke="none"
                    alignment-baseline="end"
                    class="axe_label_text"
                    transform="rotate(-90 222 -50)">
                    Altitude, km
                </text>
                <!-- x axe label -->
                <text x="105" :y="-120" dy="0.13em" text-anchor="middle" stroke="none"
                    alignment-baseline="end"
                    class="axe_label_text">
                    Distance Along the Trajectory Projection, km
                </text>

                <!-- y ticks left -->
                <path stroke="black" fill="none" stroke-width="0.5" stroke-opacity="0.5"
                    v-for="y in [10,20,30,40,50,60,70,80,90,100]" :key="'axetick-left-y' + y"
                    :d="'M0 ' + (-y)  + 'L3 ' + (-y)"/>

                <!-- y ticks right -->
                <path stroke="black" fill="none" stroke-width="0.5" stroke-opacity="0.5"
                    v-for="y in [10,20,30,40,50,60,70,80,90,100]" :key="'axetick-right-y' + y"
                    :d="'M210 ' + (-y)  + 'L207 ' + (-y)"/>


                <!-- y-text-left -->
                <text x="-2" :y="-y" dy="0.13em" text-anchor="end" stroke="none"
                    alignment-baseline="middle"
                    class="axe_tick_text"
                    v-for="y in [0,10,20,30,40,50,60,70,80,90,100]" :key="'axetick-left-text-y' + y">
                            {{y}}
                </text>
                <!-- y-text-right -->
                <text x="212" :y="-y" dy="0.13em" text-anchor="start" stroke="none"
                    alignment-baseline="middle"
                    class="axe_tick_text"
                    v-for="y in [0,10,20,30,40,50,60,70,80,90,100]" :key="'axetick-right-text-y' + y">
                            {{y}}
                </text>

                <!-- axes origin -->
                <path stroke="black" fill="none" stroke-width="0.5" stroke-opacity="0.5"
                    v-if="state.observation_point_inputs.shift_y<200"
                    :d="'M'+state.observation_point_inputs.shift_y+' -' + (top_axe_origin_y) + ' L'+state.observation_point_inputs.shift_y+' 0'"/>



                <!-- entry -->
                <g class="effects_group hover_shadow" @mouseover="markers.entry.icon.tooltip = true" @mouseout="markers.entry.icon.tooltip = false">
<!--                     <line class="sizes_line" :x1="markers.entry.map_cs.x" y1="0" :x2="markers.entry.map_cs.x" :y2="-top_axe_origin_y"></line> 
                    <line class="sizes_line" :x1="0" :y1="-markers.entry.map_cs.y" :x2="200" :y2="- markers.entry.map_cs.y"></line>    -->
                    <use class="use_marker meteoroid" 
                        height="16" width="16" xlink:href="#icon_entry" 
                        :x="markers.entry.map_cs.x" 
                        :y="-markers.entry.map_cs.y" 
                        :transform="'rotate('+ (-state.variant.angle+55)+' '+ markers.entry.map_cs.x + ' ' + (-markers.entry.map_cs.y) + ')'"></use>
                </g>


                <!-- radiation -->
                <g class="effects_group hover_shadow"  @mouseover="markers.irradiation.icon.tooltip = true" @mouseout="markers.irradiation.icon.tooltip = false">
                    <line class="sizes_line" :x1="markers.irradiation.map_cs.x" y1="0" :x2="markers.irradiation.map_cs.x" :y2="-top_axe_origin_y"></line> 
                    <line class="sizes_line" :x1="0" :y1="-markers.irradiation.map_cs.y" :x2="200" :y2="- markers.irradiation.map_cs.y"></line>   
                    <use class="use_marker" :x="markers.irradiation.map_cs.x" :y="- markers.irradiation.map_cs.y" height="16" width="16" xlink:href="#icon_irradiation"></use>
                </g>

                <!-- shock wave -->
                <g class="effects_group hover_shadow" @mouseover="markers.overpressure.icon.tooltip = true" @mouseout="markers.overpressure.icon.tooltip = false">
                    <line class="sizes_line" :x1="markers.overpressure.map_cs.x" y1="0" :x2="markers.overpressure.map_cs.x" :y2="- top_axe_origin_y"></line>
                    <line class="sizes_line" :x1="0" :y1="-markers.overpressure.map_cs.y" :x2="200" :y2="- markers.overpressure.map_cs.y"></line>
                    <use class="use_marker" :x="markers.overpressure.map_cs.x" :y="- markers.overpressure.map_cs.y" height="16" width="16" xlink:href="#icon_overpressure"></use>
                </g>
                
                <!-- intersection -->
                <g class="effects_group hover_shadow" @mouseover="markers.intersection.icon.tooltip = true" @mouseout="markers.intersection.icon.tooltip = false">
                    <use class="use_marker" :x="markers.intersection.map_cs.x" :y="- markers.intersection.map_cs.y" height="16" width="16" :xlink:href="'#icon_' + 'intersection'"></use> 
                </g>

                <foreignObject  v-for="(marker, name)  in markers" :key="'tooltip for ' + name"
                    :x="marker.map_cs.x" :y="- marker.map_cs.y" width="0" height="0" overflow="visible" >
                    <div xmlns="http://www.w3.org/1999/xhtml" class="tooltip_wrapper top" :class="marker.icon.tooltip ? 'active' : ''" 
                    :style="'zoom: ' + (100*200/client_size().width) +'%;'">
                        <div class="tooltip_back">{{$t('calculator.schema.' + marker.icon.tooltip_text_key)}}</div>
                    </div>
                </foreignObject>

            </svg>


        </svg>
    </v-col>
</template>


<script lang="ts">
import MathExt from '../../lib/MathExt';
import { Point } from '../../model/Geometry';
import State from '../../model/State';
import { Component, Prop, Vue, Model, ModelSync } from 'vue-property-decorator';
class Icon
{
    constructor(code: string, tooltip_text_key: () => string)
    {
        this.code = code;
        this.tooltip_text_key_f = tooltip_text_key;
    }
    private tooltip_text_key_f: () => string;

    code:string;
    tooltip: boolean = false;
    get tooltip_text_key(): string { return this.tooltip_text_key_f(); }
}
class Marker
{

    constructor(code: string, tooltip_text_key: () => string, map_point_getter: ()=>{x: number,  y:number})
    {
        this.icon = new Icon(code, tooltip_text_key);
        this.map_point_getter = map_point_getter;
    }
    map_point_getter: ()=> { x: number,  y:number };
    icon: Icon
    get map_cs(): {x: number, y: number} { return this.map_point_getter(); }
}

@Component
export default class Schema extends Vue {
    debug: boolean = false;
    log(...data: any[])
    {
        if(this.debug == true)
            console.log("schema ", ...data);
    }
    markers = {
        entry: new Marker("&#xe902;", () => ((this.state.variant.angle_rad < Math.atan( (100)/(200))) ? 'Entry direction':'Entry point'), () => this.false_entry),
        intersection: new Marker("&#8736;", () => 'Surface intersection point', () => {return {x: 0, y: 0}}),
        overpressure: new Marker("&#xe904;", () => 'Effective overpressure point source', 
            () => {return {
                x: this.state.effects.shock_wave.zero_point, 
                y: this.state.effects.shock_wave.heff
                }}),
        irradiation: new Marker("&#xe9a9;", () => 'Effective thermal point source', 
            () => {return {x: this.state.effects.irradiation.zero_point, y: this.state.effects.irradiation.hrad}}),

    }


    top_axe_origin_y = 110;
    get left_axe_origin(): number { return this.shift_x; }
    get shift_x(): number { return this.state.observation_point_inputs.shift_y; }
    get x_ticks(): ReadonlyArray<{pos: number, label: number}>
    {
        this.log('x_ticks_calc');
        let shift = this.state.observation_point_inputs.shift_y;
        let mod_10 = shift % 10;

        return MathExt.range(0, 200, 10).map(s => { return {pos: s  + mod_10, label: s - shift + mod_10}; }).filter(s => s.pos <= 200);
    }

    @Prop({default: 200}) size!: number;

    shock_wave_hover: boolean = false;
    radiation_hover: boolean = false;
    
    get entry(){return { x:100./Math.tan(this.state.variant.angle_rad), y: 100};}

    get false_entry(){

        let crit_angle = Math.atan( (100)/(200))/Math.PI * 180;
        if(this.state.variant.angle < crit_angle)
        {
            /* let d = ((200.-this.airburst_point.x)**2 + (200 * Math.tan(this.state.variant.angle_rad)-this.airburst_point.y) ** 2)**0.5-16;
            let x = d * Math.cos(this.state.variant.angle_rad);
            let y = d * Math.sin(this.state.variant.angle_rad);
            return { x: this.airburst_point.x + x, y: this.airburst_point.y + y }; */
            return {x:200, y: (200) * Math.tan(this.state.variant.angle_rad)};
        }
        //else return {y:100-10-10, x: (100-10-10) / Math.tan(this.state.variant.angle_rad)};
        else return this.entry;
    }
    get arrow_placement()
    {
        return {
            x: this.markers.entry.map_cs.x - 12*Math.cos(this.state.variant.angle_rad),
            y: -this.markers.entry.map_cs.y + 12*Math.sin(this.state.variant.angle_rad)  
            };
    }


    get airburst_point() {return {x: this.state.effects.shock_wave.zero_point, y: this.state.effects.shock_wave.heff};}
    get irradiation_point() {return {x: this.state.effects.irradiation.zero_point, y: this.state.effects.irradiation.hrad};}

    $refs!: {
        svg: HTMLFormElement
    }

    state: State = State.state;
    math_ext = MathExt;
    

    client_size():{width:number, height: number} {
        if(this.$refs.svg == undefined)
            return {width: 200, height: 200};
        let svg:HTMLFormElement = this.$refs.svg;
        
        return {width: svg.clientWidth, height: svg.clientHeight};
    }

    mounted(){
        this.log("mounted ", this.client_size(), this.$refs.svg.clientWidth);
        this.state = State.state;
    }
}
</script>

<style scoped lang="scss">
    
   
    .sand
    {
        stroke: #E9AB69;
    }
    .rock
    {
        stroke: #838383;
    }
    .axe_tick_text
    {
        font: 5px serif; 
        fill: rgb(29, 29, 29);
    }
    .axe_label_text
    {
        font: 8px serif; 
        fill: rgb(29, 29, 29);
    }
    .entry_vactor_arrow
    {
        font: 26px serif;
        fill: #3F51B5;
    }


</style>