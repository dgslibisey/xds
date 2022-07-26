<template>
  <v-container>
    <v-row class="text-center">
      <v-col>
        <h3>Archives of Radiology</h3>
        <template>
          <v-container fluid>
            <v-row>
              <v-col cols="12">
                <v-select
                  :value="registryUrl"
                  item-value="registryUrl"
                  :items="items"
                  :item-text="'id'"
                  label="Document Registry"
                  :v-model="'registryUrl'"
                  return-object
                  @input="registryUrl = $event ? $event : null"
                ></v-select>
              </v-col>
            </v-row>
          </v-container>
        </template>
        <br /><br /><br /><br />
        <template>
          <v-form ref="form" v-model="valid" lazy-validation>
            <v-text-field
              v-model="patientId"
              :rules="patientIdRules"
              label="Patient ID"
              required
            ></v-text-field>
            <v-btn
              type="submit"
              @click.prevent="sorgu()"
              :disabled="!valid"
              color="success"
              class="mr-4"
            >
              Submit
            </v-btn>

            <v-btn color="error" class="mr-4" @click="reset">
              Reset Form
            </v-btn>
            <!-- <input type="file" ref="file" style="display: none">
            <button @click="$refs.file.click()">open file dialog</button> -->
          </v-form>
        </template>
      </v-col>
    </v-row>
    <v-row v-if="sorguSonuc && sorguSonuc.length > 0">
      <v-col
        ><v-simple-table>
          <template v-slot:default>
            <thead>
              <tr>
                <th class="text-left">repositoryUid</th>
                <th class="text-left">documentUid</th>
                <th class="text-left">mimeType</th>                
                <th class="text-left">patient name</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in sorguSonuc" :key="item.documentUid">
                <td>{{ item.repositoryUid }}</td>
                <td>{{ item.documentUid }}</td>
                <td>{{ item.mimeType }}</td>
                <td>{{ item.patName }}</td>
                <td>
                  <v-btn
                    type="get"
                    @click.prevent="git(item)"
                    :disabled="!valid"
                    color="success"
                    class="mr-4"
                  >
                    get
                  </v-btn>
                </td>
              </tr>
            </tbody>
          </template>
        </v-simple-table></v-col
      >
      <v-col>
        <iframe
          v-if="documentUrl"
          height="100%"
          width="100%"
          :src="documentUrl"
        ></iframe>
      </v-col>
    </v-row>
  </v-container>
</template>



<script>
var axios = require("axios");

export default {
  name: "HelloWorld",
  components: {},
  props: {
    fileName: String,
    path: String,
  },

  data: () => ({
    //registryUrl: [],
    items: [
      {
        id: "xdstest__rr",
        url: "http://144.122.98.93:8080/xdstools7.7.2/sim/xdstest__registry/reg/sq",
        repositoryUrl:
          "http://144.122.98.93:8080/xdstools7.7.2/sim/xdstest__repository/rep/ret",
        wadoUrl:
          "http://144.122.98.93:8080/xdstools7.7.2/httpsim/xdstest__ids/ids/wado.ret.ids",
      },
      {
        id: "default__rr",
        url: "http://144.122.98.93:8080/xdstools7.7.2/sim/default__rr/reg/sq",
        repositoryUrl:
          "http://144.122.98.93:8080/xdstools7.7.2/sim/default__rr/rep/ret",
        wadoUrl:
          "http://144.122.98.93:8080/xdstools7.7.2/httpsim/default__ids/ids/wado.ret.ids",
      },
    ],
    valid: true,
    registryUrl: "",
    repositoryUrl: "",
    patientId: "",
    patientIdRules: [(v) => !!v || "Patient ID is required"],
    repUid: "",
    repUidRules: [(v) => !!v || "This place is required"],
    select: null,
    checkbox: false,
    sorguSonuc: [],
    wadoUrl: "",
    documentUrl: "",
  }),
  methods: {
    async sorgu() {
      console.log(this.registryUrl.url);
      console.log(this.registryUrl.repositoryUrl);

      var qs = require("qs");
      var data = qs.stringify({
        repositoryUrl: this.registryUrl.repositoryUrl,
        registryUrl: this.registryUrl.url,
        wadoUrl: this.registryUrl.wadoUrl,
        patientId: "'" + this.patientId + "'",
      });
      var config = {
        method: "post",
        url: "http://localhost:8080/XDSWeb/ITI18",
        headers: {
          "Content-Type": "application/x-www-form-urlencoded",
        },
        data: data,
      };

      const res = await axios(config);
      // console.log(res.data);
      this.sorguSonuc = res.data;
    },
    git(e) {
      //console.log(this.sorguSonuc);

      // this.$router.push(
      //   "/about?repositoryUid=" +
      //     this.sorguSonuc[0].repositoryUid +
      //     "&documentUid=" +
      //     this.sorguSonuc[0].documentUid
      // );

      if (e.mimeType === "application/dicom") {
        this.$router.push(
          `/about?repositoryUid=${e.repositoryUid}&documentUid=${e.documentUid}&repositoryUrl=${this.registryUrl.repositoryUrl}&wadoUrl=${this.registryUrl.wadoUrl}&`
        );
      }

      this.documentUrl = `http://localhost:8080/XDSWeb/ITI43?repositoryUid=${e.repositoryUid}&documentUid=${e.documentUid}&repositoryUrl=${this.registryUrl.repositoryUrl}`;
    },
    validate() {
      this.$refs.form.validate();
    },
    reset() {
      this.$refs.form.reset();
    },
    resetValidation() {
      this.$refs.form.resetValidation();
    },
    submit() {
      //if you want to send any data into server before redirection then you can do it here
      this.$router.push(
        "/about?repositoryUid=" +
          this.repositoryUid +
          "&documentUid=" +
          this.documentUid
      );
    },
  },
};
</script>
