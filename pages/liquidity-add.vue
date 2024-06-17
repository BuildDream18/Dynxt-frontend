<template>
  <div class="black pl-md-70 pt-12" >
    <div>
      <div color="black" class="mx-auto rounded-xl cream-bx-2" :loading="loading">
        <v-card-title style="border-bottom: #58473E solid 1px">
          <v-row>
            <v-col cols="9">
              <div class="px-md-7">
                <div class="text-h5 card-title white--text">ADD LIQUIDITY</div>
                <div class="grey--text">
                  <span class="sub-title">Add Liquidity to receive LP tokens</span>
                </div>
              </div>
            </v-col>
            <v-col cols="3">
              <v-btn text class="mt-7 ml-md-10" @click="showSetting=true"><v-icon dark>fas fa-sliders-h</v-icon></v-btn>
            </v-col>
          </v-row>
        </v-card-title>
        <v-divider></v-divider>
        <v-card-text class="pa-3 pa-md-7">
          <v-form class="mt-0" ref="form">
            <v-row>
              <v-col cols="12">
                <v-text-field outlined
                              label="Input"
                              color="cream darken-2"
                              hide-details
                              class="rounded-lg"
                              placeholder="0.0"
                              type="number"
                              v-model="form.from"
                              @change="formFromChange"
                              @input="fromAmountInput">
                  <template v-slot:append style="display: block !important">
                    <div class="">
                      <div class="text-center" style="padding-bottom: 15px !important; color: lightgray !important;">balance:  <span class="pl-3" style="color: white;">{{fromBalance>1e8?(fromBalance*1).toLocaleString('fullwide', { useGrouping: false }).slice(0,7)+"...":(fromBalance*1).toLocaleString('fullwide', { useGrouping: false })}}</span></div>
                      <div v-if="fromToken">
                        <v-row>
                          <v-col v-if="getIsConnected" cols="4" class="text-left">
                            <v-btn color="cream" small class="px-0" text @click="setMax0">MAX</v-btn>
                          </v-col>
                          <v-col cols="8" class="text-center pl-0 mb-5">
                            <v-btn small text class="px-0" @click="selectFromToken=true">
                              <v-img :src="getTokenItem(fromToken).image"
                                    contain
                                    height="25px"
                                    width="25px" class="my-auto mr-2"></v-img>
                              {{getTokenItem(fromToken).name}}
                            </v-btn>
                          </v-col>
                        </v-row>
                      </div>
                      <div v-else>
                        <v-btn text small class="text-center pl-0 mb-4 mt-1" @click="selectFromToken=true">
                          Select a currency
                        </v-btn>
                      </div>
                    </div>
                  </template>
                </v-text-field>
              </v-col>
              <v-col cols="12" class="text-center">
                <v-icon color="cream" class="pr-4 component-right">mdi-plus-thick</v-icon>
              </v-col>
              <v-col cols="12">
                <v-text-field outlined
                              label="Input"
                              type="number"
                              :step="0.001"
                              hide-details
                              placeholder="0.0"
                              color="cream"
                              class="rounded-lg"
                              v-model="form.to"
                              @change="formToChange"
                              @input="toAmountInput">
                  <template v-slot:append style="display: block !important">
                    <div class="">
                      <div class="text-center" style="padding-bottom: 15px !important; color: lightgray !important;">balance:  <span class="pl-3" style="color: white;">{{toBalance>1e8?(toBalance*1).toLocaleString('fullwide', { useGrouping: false }).slice(0,7)+"...":(toBalance*1).toLocaleString('fullwide', { useGrouping: false })}}</span></div>
                      <div v-if="toToken">
                        <v-row>
                          <v-col v-if="getIsConnected" cols="4" class="text-left px-0">
                            <v-btn small class="px-0" color="cream" text @click="setMax1">MAX</v-btn>
                          </v-col>
                          <v-col cols="8" class="text-center pl-0 mb-5">
                            <v-btn small text class="px-0" @click="selectToToken=true">
                              <v-img :src="getTokenItem(toToken).image"
                                    contain
                                    height="25px"
                                    width="25px" class="my-auto mr-2"></v-img>
                              {{getTokenItem(toToken).name}}
                            </v-btn>
                          </v-col>
                        </v-row>
                      </div>
                      <div v-else>
                        <v-btn text small class="text-center pl-0 mb-4 mt-1" @click="selectToToken=true">
                          Select a currency
                        </v-btn>
                      </div>
                    </div>
                  </template>
                </v-text-field>
              </v-col>
              <v-col cols="12" class="text-left text-white" style="color: white; font-weight: 600;" v-if="fromToken > 0 && toToken > 0">
                <v-row class="pa-3">
                  <v-col cols="12">
                    <small class="small-larger-font">Prices and pool share</small>
                  </v-col>
                  <v-col cols="4">
                    <div class="text--center">
                      <small class="small-larger-font">0.0000000083723</small>
                    </div>
                    <div class="text--center">
                      <small class="small-larger-font">DYNXT to BNB</small>
                    </div>
                  </v-col>
                  <v-col cols="4">
                  </v-col>
                  <v-col cols="4">
                    <div class="text--center">
                      <small class="small-larger-font">0.0000000083723</small>
                    </div>
                    <div class="text--center">
                      <small class="small-larger-font">DYNXT to BNB</small>
                    </div>
                  </v-col>
                  <v-col cols="12">
                    <div class="text--center">
                      <p class="larger-font">1.60%</p>
                    </div>
                    <div class="text--center">
                      <small class="small-larger-font">Share of pool</small>
                    </div>
                  </v-col>
                </v-row>
              </v-col>
              <v-col cols="12" v-if="getIsConnected && fromToken > 0 && toToken > 0 && form.from > 0 && form.to > 0">
                <v-btn class="rounded-lg py-7 px-4" 
                      block 
                      :color="'cream'" 
                      @click="enableDYNXT">
                        Enable DYNXT
                </v-btn>
              </v-col>
              <v-col cols="12">
                <v-btn class="rounded-lg py-7 px-4" 
                      block 
                      :disabled="getIsConnected && !(getIsConnected && form.from > 0 && form.from <= fromBalance)" 
                      :color="getIsConnected && form.from > 0 && form.to > 0?'cream': getIsConnected && form.from <= 0?'darkcream':'cream'" 
                      :style="getIsConnected && !(getIsConnected && form.from > 0 && form.from <= fromBalance)&&'background-color: #a17c49 !important'" 
                      @click="supply">
                        {{connectButton}}
                </v-btn>
              </v-col>
            </v-row>
          </v-form>
        </v-card-text>
      </div>
      <div v-show="fromToken > 0 && toToken > 0" color="black" class="mx-auto rounded-xl cream-bx-2 pa-10 mt-12" :loading="loading">
        <v-row>
          <v-col cols="12" class="text-left text-white py-1" style="color: gray; font-weight: 600;">
            <small class="smaller-font">By adding liquidity you'll earn 0.17%  of all trades on this pair proportional to your share of the pool. Fees are added to the pool.. accrue in real time and can be claimed by withdrawing your liquidity.</small>
          </v-col>
        </v-row>
      </div>
    </div>
    
    <v-dialog v-model="selectFromToken" max-width="400" content-class="pl-md-70">
      <token-list :token="fromToken"
                  @change="changeFromToken"
                  :tokenLists="tokenLists"
                  @close="selectFromToken=false"
      ></token-list>
    </v-dialog>
    <v-dialog v-model="selectToToken" max-width="400" content-class="pl-md-70">
      <token-list :token="toToken"
                  @change="changeToToken"
                  :tokenLists="tokenLists"
                  @close="selectToToken=false"
      ></token-list>
    </v-dialog>

    <v-dialog v-model="showSetting" max-width="500" content-class="pl-md-70">
      <setting-list @close="showSetting=false" @change="changeTolerance"></setting-list>
    </v-dialog>
  </div>
</template>

<script>
    import TokenList from "../components/modals/token-list";
    import SettingList from "../components/modals/setting-list";
    import '@fortawesome/fontawesome-free/css/all.css';
    import {mapGetters, mapMutations} from 'vuex';
    import web3Service from "../web3"

    export default {
      name: "liquidity-add",
      components: {TokenList, SettingList},
      props: {
        info: {
          type: Object,
          default: () => {}
        }
      },
      data() {
        return {
          selectToToken: false,
          selectFromToken: false,
          
          fromBalance: 0,
          toBalance: 0,

          form: {
            from: 0.0,
            to: 0.0
          },
          loading: false,
          text: '',
          
          toToken: 0,
          fromToken: 0,
          showSetting: false,
          tokenLists: web3Service.tokenLists,
        }
      },

      computed: {
        ...mapGetters(["getIsConnected", "getCurrentAddr", "getTolerance"]),
        connectButton() {
          if(this.getIsConnected) {
            if (this.fromToken == 0 || this.toToken == 0) {
              return "Invalid pair"
            }
            else {
              if(this.form.from > 0 && this.form.to > 0) {
                return "Supply"
              }
              else {
                return "Enter an amount"
              }
            }
          }
          else {
            return "Unlock Wallet"
          }
      },
      amountOutMin() {
        return (this.form.to * (1 - this.getTolerance/100)).toFixed(5)
      },
    },

      methods: {
        ...mapMutations(["SET_CONNECT_MODAL", "SET_TOLERANCE"]),

        setMax0() {
          this.form.from = this.fromBalance;
          this.fromAmountInput()
        },
        setMax1() {
          this.form.to = this.toBalance;
          this.toAmountInput()
        },

        formFromChange() {
          
        },
        formToChange() {
          
        },

        changeTolerance(tol){
          this.SET_TOLERANCE(tol)
        },
        
        fromAmountInput(){

        },
        toAmountInput(){
          
        },
        
        getTokenItem(id){
          if(id){
            return this.tokenLists.find(item=>item.id===id)
          }else{
            return{}
          }
        },
        changeFromToken(id) {
          this.fromToken = id;
          this.selectFromToken = false

        },
        changeToToken(id) {
          this.toToken = id;
          this.selectToToken = false

        },
        changeTolerant(e){
          console.log(e);
        },

        enableDYNXT() {

        },
        supply() {

        }
      },
    }
</script>

<style lang="scss" scope>
  .v-text-field--outlined >>> fieldset {
    border-color: rgba(192, 0, 250, 0.986);
  }
  .v-text-field__slot{
    flex: 1 9 auto !important;
    input{
      margin-top: 1.5rem;
      min-height: 1.2rem;
      padding-left: .7rem !important;
      padding-right: 1rem !important;
    }
  }
  .smaller-font {
    font-size: 70%;
    line-height: 1;
  }
  .text--center {
    text-align: center;
  }
  .small-larger-font {
    font-size: 90%;
  }
  .larger-font {
    font-size: 150%;
  }
</style>
