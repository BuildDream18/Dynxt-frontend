<template>
  <div class="black pl-md-70 pt-12" >
    <div>
      <!----------------->
      <!-- Import pool -->
      <!----------------->
      <div color="black" class="mx-auto rounded-xl cream-bx-2">
        <v-card-title style="border-bottom: #58473E solid 1px">
          <v-row>
            <v-col cols="9">
              <div class="px-md-7">
                <div class="text-h5 card-title white--text">IMPORT POOL</div>
                <div class="grey--text">
                  <span class="sub-title">Import an existing pool</span>
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
                <div class="pl-4 pt-4 pb-4 rounded-lg cursor-pointer form-border" @click="selectFromToken=true">
                  <div v-if="fromToken">
                    <v-btn small text class="px-0">
                      <v-img :src="getTokenItem(fromToken).image"
                            contain
                            height="25px"
                            width="25px" class="my-auto mr-2"></v-img>
                      {{getTokenItem(fromToken).name}}
                    </v-btn>
                    <v-icon color="cream" class="pr-4 component-right">mdi-chevron-down</v-icon>
                  </div>
                  <div v-else>
                    <v-btn text small class="mt-1">
                      Select a currency
                    </v-btn>
                  </div>
                </div>
              </v-col>
              <v-col cols="12" class="text-center">
                <v-icon color="cream">mdi-plus-thick</v-icon>
              </v-col>
              <v-col cols="12">
                <div class="pl-4 pt-4 pb-4 rounded-lg cursor-pointer form-border" @click="selectToToken=true">
                  <div v-if="toToken">
                    <v-btn small text class="px-0">
                      <v-img :src="getTokenItem(toToken).image"
                            contain
                            height="25px"
                            width="25px" class="my-auto mr-2"></v-img>
                      {{getTokenItem(toToken).name}}
                    </v-btn>
                    <v-icon color="cream" class="pr-4 component-right">mdi-chevron-down</v-icon>
                  </div>
                  <div v-else>
                    <v-btn text small class="mt-1">
                      Select a currency
                    </v-btn>
                  </div>
                </div>
              </v-col>
              <v-col cols="12">
                <div class="mt-5 mb-5">
                  <p class="txt-white">Select A Token To Find Your Liquidity</p>
                </div>
              </v-col>
            </v-row>
          </v-form>
        </v-card-text>
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
  import web3Service from "../web3";
  import TokenList from "../components/modals/token-list";
  import SettingList from "../components/modals/setting-list";

  export default {
    name: "liquidity-import-pool",
    components: {TokenList, SettingList},

    data() {
      return {
        selectToToken: false,
        selectFromToken: false,
        
        showSetting: false,

        fromToken: 1,
        toToken: 0,
        tokenLists: web3Service.tokenLists,
      }
    },

    methods: {
      getTokenItem(id){
        if(id){
          return this.tokenLists.find(item=>item.id===id)
        }else{
          return{}
        }
      },
      formFromChange() {
            
      },
      formToChange() {
        
      },
      changeFromToken(id) {
        this.fromToken = id;
        this.selectFromToken = false
      },
      changeToToken(id) {
        this.toToken = id;
        this.selectToToken = false
      },
    }
  }
</script>

<style lang="scss" scope>
  .txt-white {
    text-align: center; 
    color: white;
    font-size: 14px;
  }
  .form-border {
    border: thin solid #FFC473;
  }
  .cursor-pointer {
    cursor: pointer;
  }
  .component-right {
    float:right;
  }
</style>
