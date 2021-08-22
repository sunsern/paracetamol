<template>
  <v-container fluid>
    <v-row>
      <v-col><h2>Psi Calculator</h2></v-col>
    </v-row>
    <v-row>
      <v-col cols="12" md="4">
        <v-card>
          <v-card-text>
            <v-row>
              <v-col>
                <v-text-field
                  label="APAP @ 4h"
                  suffix="ug/mL"
                  type="number"
                  v-model="apap4"
                ></v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <v-text-field
                  label="t_NAC"
                  suffix="h"
                  type="number"
                  v-model="tnac"
                ></v-text-field>
              </v-col>
            </v-row>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="12" md="4">
        <v-expansion-panels>
          <v-expansion-panel>
            <v-expansion-panel-header>
              Advanced Settings
            </v-expansion-panel-header>
            <v-expansion-panel-content>
              <v-row>
                <v-col>
                  <v-text-field
                    label="Ti"
                    suffix="h"
                    type="number"
                    v-model="ti"
                  ></v-text-field>
                </v-col>
              </v-row>
              <v-row>
                <v-col>
                  <v-text-field
                    label="APAP threshold"
                    suffix="ug/mL"
                    type="number"
                    v-model="apap_threshold"
                  ></v-text-field>
                </v-col>
              </v-row>
            </v-expansion-panel-content>
          </v-expansion-panel>
        </v-expansion-panels>
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="6" md="2">
        <v-subheader>Psi Parameter</v-subheader>
      </v-col>
      <v-col cols="6" md="2">
        <v-text-field
          label="N/A"
          :value="psi_param"
          solo
          readonly
        ></v-text-field>
      </v-col>
    </v-row>
  </v-container>
</template>
<script>
export default {
  data() {
    return {
      apap4: null,
      tnac: null,
      ti: 6,
      apap_threshold: 45,
    };
  },
  layout: "fullpage",
  computed: {
    psi_param() {
      if (
        this.apap4 === null ||
        this.tnac === null ||
        isNaN(this.apap4) ||
        isNaN(this.tnac)
      )
        return "";
      let k3 = Math.log(2) / 4.0;
      let w = Math.log((2 * this.apap4) / this.apap_threshold) / k3;
      let tf = Math.min(this.tnac, w);
      let first_term =
        (2 * this.apap4 * (Math.exp(-k3 * this.ti) - Math.exp(-k3 * tf))) / k3;
      let second_term = this.apap_threshold * (tf - this.ti);
      let final = (first_term - second_term) / 150;
      return final > 0.001 ? final.toFixed(3) : 0.001;
    },
  },
};
</script>