<template>
	
    <v-row no-gutters align="center">
        <div class="calculator_input input_text italic_prefix">
            <v-text-field
                v-model.lazy="display_value" 
                @blur="is_focused = false"
                @focus="is_focused = true"
                required                
                :rules="rules"
                :prefix="prefix"
                v-on:input="test_text_input"
            >
            <template v-slot:label>
                <span v-html="label"></span> (<span v-html="dimension"></span>)
            </template>
        </v-text-field>
            
        </div>
        <v-col no-gutters align-self="end">
                <div class="slider_wrapper">
                    <v-slider
                        v-model="slider_value"
                        :min="log_slider ? 0 : min"
                        :max="log_slider ? 1 : max"
                        :step="log_slider ? 0.01 : accuracy"
                        v-on:input="test_slider_input"
                        >                        
                    </v-slider>
                    <div class="inside">
                        <div class="inside_col">{{min}}&nbsp;<span v-html="dimension"></span></div>
                        <div class="intermediate"></div>
                        <div class="inside_col">{{max}}&nbsp;<span v-html="dimension"></span></div>
                    </div>
                </div>
        </v-col>
        <help :help_title="help_title" :help_text="help_text">
            <template>
                <slot name="help"/>
            </template>
        </help>
    </v-row>
</template>

<script lang="ts">
import { Component, Prop, Vue, Model, ModelSync, Watch } from 'vue-property-decorator';
import MathExt from '@/components/lib/MathExt';
import Help from './help.vue'
@Component({
  components: {
      Help
  },
})
export default class FieldInput extends Vue {

    debug: boolean = false;
    log(...data: any[])
    {
        if(this.debug == true)
            console.log("id:",this.id," ", ...data);
    }

        
    @ModelSync('value', 'change', { })  outer_value!: number

    hidden_local_value: number = 0;
    delayed_set_local_value: boolean = false;
    get local_value() { return this.hidden_local_value; }
    set local_value(v: number) 
    {
        //console.log("start delayed set");
        this.log("setting local value");
        this.hidden_local_value = v;
        this.outer_value = this.hidden_local_value;
        return;
        if(!this.delayed_set_local_value)
        {
            this.delayed_set_local_value = true;

            setTimeout(()=> {
                this.log("delayed set");
                this.outer_value = this.hidden_local_value;
                this.delayed_set_local_value = false;
            }, 1000);
        }
    }
    test_text_input()
    {
        this.log('test_text_input');
    }
    test_slider_input()
    {
        this.log('test_slider_input');
    }
    is_focused: boolean = false;

    //visual
    @Prop({default: ""}) help_text!: string;
    @Prop({default: ""}) help_title!: string;
    @Prop({default: "-"}) id!: string;
    @Prop({default: 'value'}) label!: string;
    @Prop({default: ''}) dimension!: string;
    @Prop({default: ''}) prefix!: string;
    @Prop({default: () => []}) rules!: ((v:any) => string|true)[];

    //accuracy
    @Prop({default: 0.1}) accuracy!: number;
    @Watch('accuracy')
    on_accuracy_changed(val: number, old_val: number)
    {
        this.accuracy_digits = -Math.log10(this.accuracy);
        this.log("accuracy_digits", this.accuracy_digits);
    }
    accuracy_digits: number = 1;

    is_mounted = false;
    mounted()
    {
        this.log("mounted", this.outer_value);
        this.on_accuracy_changed(0,0);
        this.on_value_changed(this.outer_value,this.outer_value);
        this.is_mounted = true;
    }
    lost_focus() {
        this.log('blured');
        this.display_value = this.setted_display_value;
    }

    @Watch('value')
    on_value_changed(val: number, old_val: number)
    {
        this.log("on_value_changed", val, this.display_value, this.hidden_local_value);
        if(val != this.setted_value && !this.is_focused)
        {
            this.log("on_value_changed setting", val);
            let res = Math.round(val / this.accuracy)*this.accuracy;


            this.setted_value = res;
            this.setted_display_value = MathExt.round_decimal_digits_to_string(this.setted_value, this.accuracy_digits);
            this.display_value = this.setted_display_value;
            if(this.min <= res && res <= this.max)
            {
                this.hidden_local_value = res;
            }
            this.log("on_value_changed setting ends",res,this.setted_value, this.local_value);
        }
    }


    setted_display_value: string = '';
    setted_value: number = 0;
    display_value: string = '';
    @Watch('display_value')
    on_display_value_changed(val: string, old_val: string)
    {
        this.log("on_display_value_changed ", val);
        if(val != this.setted_display_value)
        {
            let parsed = parseFloat(val);
            this.log("isNaN(parsed)", isNaN(parsed));
            if(!isNaN(parsed))
            {
                let res = Math.round(parsed / this.accuracy)*this.accuracy;
                this.log("rounded res:", res);
                if(this.setted_value != res)
                {
                    if(this.min<= res && res <= this.max)
                    {
                        this.setted_value = res;
                        this.setted_display_value =  MathExt.round_decimal_digits_to_string(this.setted_value, this.accuracy_digits);
                        this.local_value = this.setted_value;
                        //this.$emit('change', this.setted_value);
                    }
                }
            }
        }
    }
    @Watch('min')
    on_min_changed(val: number, old_val: number)
    {
        
        this.log("on_min_changed",val, this.min, this.outer_value);
    }
    //slider
    @Prop({default: 0}) min!: number;
    @Prop({default: 100}) max!: number;
    @Prop({default: false}) log_slider!: boolean;
    private log_base: number = 0.02;

    //get slider_value() : number { return this.log_slider ? Math.log(this.hidden_local_value)/Math.log(this.log_base) : this.hidden_local_value; }
    get slider_value() : number { return this.log_slider ? 
        (Math.log(this.log_base + (this.hidden_local_value-this.min)/(this.max-this.min)) - Math.log(this.log_base))/(Math.log(this.log_base+1)-Math.log(this.log_base))
        : this.hidden_local_value; }
    set slider_value(val: number) { 
        //console.log(val + " " + Math.round(Math.pow(this.log_base, val)));
        if(!this.is_mounted) return;
        this.log("slider_value set", val);
        if(this.log_slider)
        {
            //this.local_value = Math.round(Math.pow(this.log_base, val));
            let res = this.min + (this.max-this.min)*(Math.exp(val * (Math.log(this.log_base+1) - Math.log(this.log_base)) + Math.log(this.log_base)) - this.log_base);
            this.local_value = Math.round(res * 10)/10;
        } 
        else 
            this.local_value = Math.round(val * 10)/10;
    }

}
</script>
<style scoped lang="scss">
    .input_text{
        width: 100px;
    }
    .v-text-field__prefix{
        font-style: italic;
    }
    .slider_wrapper{
        margin-left: 12px;
        position: relative;
    }
    .inside{
        position: absolute;
        top: 20px;
        width: 100%;
        display: flex;
        font-size: 10px;
        .intermediate{
            display: flex;
            flex-grow: 1;
            flex-direction: row;
        }
        .inside_col{
            display: flex;
            flex-grow: 0;
            flex-direction: row;
        }
    }
</style>