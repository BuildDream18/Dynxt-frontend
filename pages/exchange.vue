<template>
  <div class="black pl-md-70 pt-12" >
    <div>
      <div color="black" class="mx-auto rounded-xl cream-bx-2" :loading="loading">
        <v-card-title style="border-bottom: #58473E solid 1px">
          <v-row>
            <v-col cols="9">
              <div class="px-md-7">
                <div class="text-h5 card-title white--text">XCHANGE</div>
                <div class="grey--text">
                  <span class="sub-title">Trade tokens in an instant</span>
                </div>
              </div>
            </v-col>
            <v-col cols="3">
              <!-- <v-img :src="require('~/assets/images/settings-white.png')"
                            contain
                            height="30px"
                            width="30px" class="mx-auto mt-5"></v-img>
              <v-icon dark>fas fa-sliders-h</v-icon> -->
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
                              label="From"
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
                      <v-row>
                        <v-col v-if="getIsConnected" cols="4" class="text-left">
                          <v-btn color="cream" small class="px-0" text @click="setMax0">MAX</v-btn>
                        </v-col>
                        <!-- <v-col v-if="fromToken" cols="1" class="text-left"></v-col> -->
                        <v-col cols="8" class="text-center pl-0 mb-5">
                          <!-- @click="selectFromToken=true" -->
                          <div v-if="fromToken">
                            <v-btn small text class="px-0">
                              <v-img :src="getTokenItem(fromToken).image"
                                    contain
                                    height="25px"
                                    width="25px" class="my-auto mr-2"></v-img>
                              {{getTokenItem(fromToken).name}}
                            </v-btn>
                          </div>
                          <div v-else>
                            <v-btn text small class="mt-1">
                              Select a currency
                            </v-btn>
                          </div>
                        </v-col>
                      </v-row>
                    </div>
                  </template>
                </v-text-field>
              </v-col>
              <v-col cols="12" class="text-center">
                <v-btn icon @click="swapPosition">
                  <v-icon color="cream">mdi-arrow-up-down-bold</v-icon>
                </v-btn>

              </v-col>
              <v-col cols="12">
                <!-- :rules="rules.requiredRules" -->
                <v-text-field outlined
                              label="To"
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
                      <v-row>
                        <v-col v-if="getIsConnected" cols="4" class="text-left px-0">
                          <v-btn small class="px-0" color="cream" text @click="setMax1">MAX</v-btn>
                        </v-col>
                        <!-- <v-col v-if="toToken" cols="1" class="text-left"></v-col> -->
                        <v-col cols="8" class="text-center pl-0 mb-5">
                          <div v-if="toToken">
                            <v-btn small text class="px-0">
                              <v-img :src="getTokenItem(toToken).image"
                                    contain
                                    height="25px"
                                    width="25px" class="my-auto mr-2"></v-img>
                              {{getTokenItem(toToken).name}}
                            </v-btn>
                          </div>
                          <div v-else>
                            <v-btn text small class="mt-1">
                              Select a currency
                            </v-btn>
                          </div>
                        </v-col>
                      </v-row>
                    </div>
                  </template>
                </v-text-field>
              </v-col>
              <v-col cols="9" class="text-left text-white" style="color: white; font-weight: 600;">
                Slippage Tolerance{{getIsConnected}}
              </v-col>
              <v-col cols="3" class="text-right">
                {{getTolerance}}%
              </v-col>
              <v-col cols="12">
                <v-btn class="rounded-lg py-7 px-4" :disabled="getIsConnected&&!(getIsConnected && form.from > 0 && form.from <= fromBalance)" :color="getIsConnected && form.from > 0 && form.to > 0?'cream': getIsConnected && form.from <= 0?'darkcream':'cream'" style="width: fit-content; font-weight: 600;" :style="getIsConnected&&!(getIsConnected && form.from > 0 && form.from <= fromBalance)&&'background-color: #a17c49 !important'" @click="exchange">{{connectButton}}</v-btn>
              </v-col>
            </v-row>
          </v-form>
        </v-card-text>
      </div>
      <div v-show="form.from > 0 && form.to > 0" color="black" class="mx-auto rounded-xl cream-bx-2 pa-12 mt-12" :loading="loading">
        <v-row>
          <v-col cols="5" class="text-left text-white py-1" style="color: gray; font-weight: 600;">
            <small>Minimum recieved</small>
          </v-col>
          <v-col cols="7" class="text-right py-1" style="color: white; font-weight: 600;">
            {{amountOutMin}} {{tokenLists[toToken-1].name}}
          </v-col>
          <v-col cols="6" class="text-left text-white py-1" style="color: gray; font-weight: 600;">
            <small>Price impact</small>
          </v-col>
          <v-col cols="6" class="text-right py-1" style="color: white; font-weight: 600;">
            &lt; 0.01%
          </v-col>
          <v-col cols="6" class="text-left text-white py-1" style="color: gray; font-weight: 600;">
            <small>Liquidity Provider Fee</small>
          </v-col>
          <v-col cols="6" class="text-right py-1" style="color: white; font-weight: 600;">
            {{provider_fee * form.from / 100}} {{tokenLists[fromToken-1].name}}
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
    import Web3 from 'web3';
    import WalletConnectProvider from "@walletconnect/web3-provider";

    import TokenList from "../components/modals/token-list";
    import SettingList from "../components/modals/setting-list";
    import '@fortawesome/fontawesome-free/css/all.css'
    // import setting from "../../assets/images/setting.png"
    import {mapGetters, mapMutations} from 'vuex'
    import web3Service from "../web3"
    // import Moralis from 'moralis';

    export default {
        name: "exchange",
        components: {TokenList, SettingList},
        props: {
          info: {
            type: Object,
            default: () => {}
          }
        },
        data() {
          return {
            isSwaped: false,
            selectToToken: false,
            selectFromToken: false,
            
            intervalUpdate: null,
            connectModal: false,
            web3: null,
            shortAddr: '',
            // getIsConnected: false,
            isWalletConnectProtocol: false,
            tokenContract: null,
            bnbContract: null,
            cryptoContract: null,
            presaleContract: null,

            saleRate: "1250M DYNXT/BNB",

            totalSold: 0,

            fromBalance: 0,
            toBalance: 0,

            form: {
              from: 0.0,
              to: 0.0
            },
            loading: false,
            snackbar: false,
            text: '',
            rules: {
              requiredRules: [v => !!v || 'This field is required'],
              emailRules: [
                v => !!v || 'E-mail is required',
                v => /.+@.+/.test(v) || 'E-mail must be valid',
              ],
            },
            toToken: 5,
            fromToken: 1,
            showSetting: false,
            tokenLists: web3Service.tokenLists,
            inputCounts: 0,
            provider_fee: 0.25
          }
        },

        mounted: async function() {

          // Moralis.initialize("YOUR_APP_ID", "", "YOUR_MASTERKEY");
          // get a web3 instance for a specific chain

          // const web3 = Moralis.web3ByChain("0x1"); // mainnet

          // const web3 = new Moralis.Web3('https://data-seed-prebsc-1-s1.binance.org/');
          // const FACTORY_ABI = [{"inputs":[{"internalType":"address","name":"_feeToSetter","type":"address"}],"payable":false,"stateMutability":"nonpayable","type":"constructor"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"address","name":"token0","type":"address"},{"indexed":true,"internalType":"address","name":"token1","type":"address"},{"indexed":false,"internalType":"address","name":"pair","type":"address"},{"indexed":false,"internalType":"uint256","name":"","type":"uint256"}],"name":"PairCreated","type":"event"},{"constant":true,"inputs":[],"name":"INIT_CODE_PAIR_HASH","outputs":[{"internalType":"bytes32","name":"","type":"bytes32"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"internalType":"uint256","name":"","type":"uint256"}],"name":"allPairs","outputs":[{"internalType":"address","name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"allPairsLength","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"internalType":"address","name":"tokenA","type":"address"},{"internalType":"address","name":"tokenB","type":"address"}],"name":"createPair","outputs":[{"internalType":"address","name":"pair","type":"address"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"feeTo","outputs":[{"internalType":"address","name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"feeToSetter","outputs":[{"internalType":"address","name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"internalType":"address","name":"","type":"address"},{"internalType":"address","name":"","type":"address"}],"name":"getPair","outputs":[{"internalType":"address","name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"internalType":"address","name":"_feeTo","type":"address"}],"name":"setFeeTo","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":false,"inputs":[{"internalType":"address","name":"_feeToSetter","type":"address"}],"name":"setFeeToSetter","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"}]
          // const FACTORY_ADDRESS = "0x8F9ce3b55e564586a9AaE9a37850bbf70f560F2c"

          // // create contract instance
          // const contract = new web3.eth.Contract(FACTORY_ABI, FACTORY_ADDRESS);

          // // get contract name
          // const res = await contract.methods
          //   .getPair("0xae13d989dac2f0debff460ac112a837c89baa7cd", "0x20fc2979e9c229a19710692299677f21d623887d")
          //   .call()
          //   .catch((e) => console.log(e));

          // console.log("pair add111: " + res);

          // this.update()
          // this.interval = setInterval(() => {
          //   this.update()
          // }, 10000);
        },

        computed: {
          ...mapGetters(["getIsConnected", "getCurrentAddr", "getTolerance"]),
          connectButton() {
            if(this.getIsConnected) {
              if(this.form.from > 0) {
                if(this.form.to === 0) {
                  return "Insufficient liquidity for this trade."
                }
                else if(this.form.from > this.fromBalance) {
                  return "Insufficient "+this.tokenLists[this.fromToken-1].symbol+" balance"
                }
                else {
                  return "Swap"
                }
              }
              else{
                return "Enter an amount"
                // this.form.from = "0.0"
              }
            }
            else{
              return "Unlock Wallet"
            }
          },
          amountOutMin() {
            return (this.form.to * (1 - this.getTolerance/100)).toFixed(5)
          },
        },

        methods: {
          ...mapMutations(["SET_CONNECT_MODAL", "SET_TOLERANCE"]),

          // update() {
          //   if(this.getIsConnected){
          //     web3Service.getBalance(this.fromToken).then(res=>{
          //       // console.log("YOUR fromToken Balance: " + web3Service.your_balance[this.fromToken]/1e18);
          //       this.fromBalance = res==0?0:(res*1).toFixed(2);
          //     })
          //     web3Service.getBalance(this.toToken).then(res=>{
          //       // console.log("YOUR toToken Balance: " + web3Service.your_balance[this.toToken]/1e9);
          //       this.toBalance = res==0?0:(res*1).toFixed(2);
          //     })
          //   }
          // },
          // toPlainString(num) {
          //   return (''+ +num).replace(/(-?)(\d*)\.?(\d*)e([+-]\d+)/,
          //     function(a,b,c,d,e) {
          //       return e < 0
          //         ? b + '0.' + Array(1-e-c.length).join(0) + c + d
          //         : b + c + d + Array(e-d.length+1).join(0);
          //     });
          // },
          setMax0() {
            // web3Service.getBalance(this.fromToken).then(res=>{
              // console.log("YOUR fromToken Balance: " + web3Service.your_balance[this.fromToken]/1e18);
              this.form.from = this.fromBalance;
              this.fromAmountInput()
            // })
          },
          setMax1() {
            // web3Service.getBalance(this.toToken).then(res=>{
              // console.log("YOUR toToken Balance: " + web3Service.your_balance[this.toToken]/1e9);
              this.form.to = this.toBalance;
              this.toAmountInput()
            // })
          },

          formFromChange() {
            
          },
          formToChange() {
            
          },
            // const desiredCoin = '0x0231f91e02DebD20345Ae8AB7D71A41f8E140cE7';
            // const bnb = '0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c';
            // const pairAddress = [bnb, desiredCoin];
            // //use https://bscscan.com/unitconverter to input bnb amount.
            // const amountIn = 0.1
            // const amounts = await this.dyContract.methods.getAmountsOut(
            //     amountIn,
            //     pairAddress
            // );
            // const slippage = 0.5
            // const amountOutMin = amounts[1].sub(amounts[1].div(slippage)); //slipapge set here 
            // console.log('Calculated Amounts out: ' + amountOutMin);
            // const to = bnbwalletAdress;
            // const deadline = Math.floor(Date.now() / 1000) + 60 * 10; //this represents 10 mins of deadline, change to ur liking.
            // const tx = await this.dyContract.methods.swapExactETHForTokens(
            //     amountOutMin,
            //     pairAddress,
            //     to,
            //     deadline,
            //     {value: 1, gasPrice: 10e9}
            // )
            // console.log('Transaction Submitted, heres the hashcode '+ tx.hash)
            // const receipt = await tx.wait();
            // console.log(receipt);
            // this.dyContract.methods.swapExactETHForTokens(0, ).call().then(res => {
            //   console.log("current Balance123: " + res/1e18);
            //   this.toBalance = (res/1e24).toFixed(2);
            // })
          // },
          changeTolerance(tol){
            this.SET_TOLERANCE(tol)
          },
          
          fromAmountInput(){
            this.inputCounts++
            // if(this.form.from <= 0 || this.form.from == "") {
            //   this.form.from = 0
            //   this.form.to = 0
            //   console.log(isNaN(this.form.from));
            //   console.log(this.form.from<0 || isNaN(this.form.from));
            // }
            // else{
            web3Service.getAmountsOut(this.form.from, this.fromToken, this.toToken, this.inputCounts)
            .then(res=>{
              // console.log(res[1]/this.tokenLists[this.toToken-1].decimals);
              if(res.seq == this.inputCounts && res.res && !(res.res[0].length<2 && res.res[1].length<2)) {
                if(res.res.length)
                  this.form.to = res.res[1]/this.tokenLists[this.toToken-1].decimals //(fromBalance*1).toLocaleString('fullwide', { useGrouping: false })
                else
                  this.form.to = 0
              }
              else {
                this.form.to = 0
              }
            })
            // }
          },
          toAmountInput(){
            console.log("input", this.form.to);
            this.inputCounts++
            // if(this.form.to < 0 || this.form.to == "") {
            //   this.form.from = 0
            //   this.form.to = 0
            //   console.log(this.form.to);
            // }
            // else {
            web3Service.getAmountsIn(this.form.to, this.fromToken, this.toToken, this.inputCounts)
            .then(res=>{
              // console.log(res[1]/this.tokenLists[this.fromToken-1].decimals);
              if(res.seq == this.inputCounts && res.res && !(res.res[0].length<2 && res.res[1].length<2)) {
                if(res.res.length)
                  this.form.from = res.res[0]/this.tokenLists[this.fromToken-1].decimals
                else
                  this.form.from = 0
              }
              else {
                this.form.from = 0
              }
            })
            // }
          },
          
          exchange() {
            console.log(this.form.from, this.fromToken, this.toToken);
            if (!this.getIsConnected) {
              this.SET_CONNECT_MODAL()
            } else {
              if(this.form.from == 0){
                alert("Amount of BNB must be more than zero");
                return;
              }
              web3Service.exchange(this.form.from, this.amountOutMin, this.fromToken, this.toToken)
              .then(res=>{
                console.log("exchange successed!");
              })
              .catch(e => console.log(e))
              // this.presaleContract.methods.buyToken().send({ from: this.currentAddr, value: this.form.from * 1e18 }, function (res) {
              //   if (res != null) {
              //     console.log("Buy Token Successfully")
              //   }
              // })
            }
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
          swapPosition(){
            let fromToken=this.fromToken;
            let from=this.form.from;
            let fromBalance=this.fromBalance;
            this.fromToken=this.toToken;
            this.form.from=this.form.to;
            this.form.to=from;
            this.toToken=fromToken;
            this.fromBalance=this.toBalance;
            this.toBalance=fromBalance;
            this.isSwaped = !this.isSwaped;
          },
          changeTolerant(e){
            console.log(e);
          }
        },
        watch: {
          getIsConnected(val) {
            // let fromToken_bal = 0
            // let toToken_bal = 0

            if(val) {
              /**GET BNB BALANCE OF ACCOUNT */
              const params = {
                chain: "bsc",
              }
              this.$axios
              .setHeader('X-API-Key', '7xA1dBDe9HpxOqfrJDGANjNkeBjLvh3BwyXsoAcxcM6rjOj1HM5fp0kMW7NdOkQl')
              this.$axios
              .$get('https://deep-index.moralis.io/api/v2/'+this.getCurrentAddr+'/balance', {
                params: params
              })
              .then(res => {
                console.log("here", res);
                if(this.fromToken == 1) {
                  console.log(parseInt(res.balance));
                  console.log(this.tokenLists[this.fromToken-1].decimals);
                  this.fromBalance = parseInt(res.balance)/this.tokenLists[this.fromToken-1].decimals
                }
                else {
                  this.toBalance = parseInt(res.balance)/this.tokenLists[this.toToken-1].decimals
                }
              })
              .catch(e => {
                const res = e
                console.log(res);
                if(this.fromToken == 1) {
                  this.fromBalance = 0
                }
                else {
                  this.toBalance = 0
                }
              })

              const params1 = {
                chain: "bsc",
              }
              /**GET BNB BALANCE OF ACCOUNT */
              this.$axios
              .$get('https://deep-index.moralis.io/api/v2/'+this.getCurrentAddr+'/erc20', {
                params: params1
              })
              .then(res => {
                console.log("here", res);
                const res_item = res.filter(val => {
                  val.token_address == web3Service.DYNXT_TOKEN_ADDRESS
                })
                if(res_item.length) {
                  if(this.fromToken == 5) {
                    // console.log(parseInt(res.balance));
                    console.log(this.tokenLists[this.fromToken-1].decimals);
                    this.fromBalance = parseInt(res_item[0].balance)/this.tokenLists[this.fromToken-1].decimals
                  }
                  else {
                    this.toBalance = parseInt(res_item[0].balance)/this.tokenLists[this.toToken-1].decimals
                  }
                }
                else {
                  if(this.fromToken == 5) {
                    this.fromBalance = 0
                  }
                  else {
                    this.toBalance = 0
                  }
                }
              })
              .catch(e => {
                const res = e
                console.log(res);
                if(this.fromToken == 5) {
                  this.fromBalance = 0
                }
                else {
                  this.toBalance = 0
                }
              })
            }
            else {
              this.fromBalance = 0
              this.toBalance = 0
            }
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
      // position: absolute !important;
      // top: 48px;
      margin-top: 1.5rem;
      min-height: 1.2rem;
      padding-left: .7rem !important;
      padding-right: 1rem !important;
    }
  }
</style>
