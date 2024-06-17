<template>
  <div class="black pl-md-70 pt-12" >
    <div>
      <!-------------------->
      <!-- Your Liquidity -->
      <!-------------------->
      <div color="black" class="mx-auto rounded-xl cream-bx-2" :loading="loading">
        <v-card-title style="border-bottom: #58473E solid 1px">
          <v-row>
            <v-col cols="9">
              <div class="px-md-7">
                <div class="text-h5 card-title white--text">YOUR LIQUIDITY</div>
                <div class="grey--text">
                  <span class="sub-title">Remove liquidity to receive tokens back</span>
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
                <div class="mt-12 mb-12">
                  <div class="mt-12 mb-12">
                    <p class="txt-gray">No Liquidity Found</p>
                    <p class="txt-gray">Don't see a pool you joined?</p>
                    <div class="mt-8 align-center">
                      <v-btn class="rounded-lg py-2 px-2 lp-button" @click="importPool">Find other LP tokens</v-btn>
                    </div>
                  </div>
                </div>
              </v-col>
              <v-col cols="12">
                <v-btn class="rounded-lg py-7 px-4" :color="'cream'" style="font-weight: 600;" block @click="addLiquidity">+ Add Liquidity</v-btn>
              </v-col>
            </v-row>
          </v-form>
        </v-card-text>
      </div>
    </div>

    <v-dialog v-model="showSetting" max-width="500" content-class="pl-md-70">
      <setting-list @close="showSetting=false" @change="changeTolerance"></setting-list>
    </v-dialog>

  </div>
</template>

<script>
  import SettingList from "../components/modals/setting-list";

  export default {
    name: "liquidity",
    components: {SettingList},

    data() {
      return {
        showSetting: false,
      }
    },

    methods: {
      addLiquidity() {
        this.$router.push("/liquidity-add");
      },

      importPool() {
        this.$router.push("/liquidity-import-pool");
      }
    }
  }
</script>

<style lang="scss" scope>
  .txt-gray {
    text-align: center; 
    color: gray;
    font-size: 12px;
  }
  .lp-button {
    font-size: 12px !important;
    border: thin solid #FFC473 !important;
  }
  .align-center {
    text-align: center; 
  }
</style>
