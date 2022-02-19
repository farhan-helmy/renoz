<template>
  <div>
    <v-container>
      <v-row>
        <v-col>
          <v-card class="mx-auto" max-width="1000" tile>
            <v-img
              height="200"
              src="https://www.w3schools.com/howto/img_nature_wide.jpg"
            ></v-img>
            <v-card-actions>
              <v-list-item class="grow">
                <v-list-item-avatar size="100">
                  <img :src="src" alt="John" />
                </v-list-item-avatar>

                <v-list-item-content>
                  <v-list-item-title>
                    <h1>{{ service.service_name }}</h1>
                  </v-list-item-title>
                  <v-list-item-title>
                    {{ service.description }}
                  </v-list-item-title>
                  <v-list-item-title> </v-list-item-title>
                  <v-list-item-title> </v-list-item-title>
                  <v-list-item-title> </v-list-item-title>
                </v-list-item-content>
                <v-row align="center" justify="end">
                  <v-dialog v-model="dialog" persistent max-width="400px">
                    <template v-slot:activator="{ on, attrs }">
                      <v-btn
                        color="red"
                        dark
                        v-bind="attrs"
                        v-on="on"
                        class="mx-2"
                      >
                        Edit
                      </v-btn>
                    </template>
                    <v-card>
                      <v-card-title>
                        <span class="text-h5">Edit service</span>
                      </v-card-title>
                      <v-card-text>
                        <v-container>
                          <v-row>
                            <v-col cols="12" sm="6" md="6">
                              <v-text-field
                                label="Name"
                                required
                                v-model="service.service_name"
                              ></v-text-field>
                            </v-col>
                            <v-col cols="12" sm="6" md="6">
                              <v-text-field
                                label="Description"
                                required
                                v-model="service.description"
                              ></v-text-field>
                            </v-col>
                          </v-row>
                        </v-container>
                      </v-card-text>
                      <v-card-actions>
                        <v-spacer></v-spacer>
                        <v-btn
                          color="blue darken-1"
                          text
                          @click="dialog = false"
                        >
                          Close
                        </v-btn>
                        <v-btn
                          color="blue darken-1"
                          text
                          @click="updateService"
                        >
                          Save
                        </v-btn>
                      </v-card-actions>
                    </v-card>
                  </v-dialog>
                  <v-dialog v-model="dialog2" persistent max-width="400px">
                    <template v-slot:activator="{ on, attrs }">
                      <v-btn
                        color="indigo"
                        dark
                        v-bind="attrs"
                        v-on="on"
                        class="mx-0"
                      >
                        Upload Pic
                      </v-btn>
                    </template>
                    <v-card>
                      <v-card-title>
                        <span class="text-h5">Upload Picture</span>
                      </v-card-title>
                      <v-card-text>
                        <v-container>
                          <v-row>
                            <v-col cols="12">
                              <v-file-input
                                accept="image/png, image/jpeg, image/bmp"
                                placeholder="Pick an avatar"
                                prepend-icon="mdi-camera"
                                label="Upload image"
                                @change="currImage"
                              ></v-file-input>
                            </v-col>
                          </v-row>
                        </v-container>
                      </v-card-text>
                      <v-card-actions>
                        <v-spacer></v-spacer>
                        <v-btn
                          color="blue darken-1"
                          text
                          @click="dialog = false"
                        >
                          Close
                        </v-btn>
                        <v-btn color="blue darken-1" text @click="uploadImage">
                          Upload
                        </v-btn>
                      </v-card-actions>
                    </v-card>
                  </v-dialog>
                </v-row>
              </v-list-item>
            </v-card-actions>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script>
export default {
  layout: "admin",
  data: () => ({
    service: [],
    dialog: false,
    dialog2: false,
    currentImage: undefined,
    service_image: "",
    src: "",
    update_service: [],
  }),
  async fetch() {
    const result = await this.$axios.$get(
      `/v1/services/${this.$route.params.id}`
    );
    console.log(result);
    this.service = result;
    await this.getImage();
    //this.tenantcount = result.tenant.properties.length
  },
  methods: {
    async getImage() {
      if (this.service.picture_link != undefined) {
        this.src = `http://localhost:3001/v1/images/${this.service.picture_link}`;
      }else{
          this.src = 'nth'
      }
    },
    async updateService() {
      //console.log("test");
      this.update_service = {
        service_name: this.service.service_name,
        description: this.service.description,
      };
      try {
        const result = await this.$axios.$patch(
          `/v1/services/${this.$route.params.id}`,
          this.update_service
        );
        this.dialog = false;
        console.log(result);
      } catch (e) {
        console.log(e);
      }
    },
    async currImage(image) {
      console.log(image);
      this.currentImage = image;
    },
    async uploadImage() {
      const image = this.currentImage;
      let fd = new FormData();
      fd.append("image", this.currentImage);
      const result = await this.$axios.$post(
        `/v1/services/picture/${this.$route.params.id}`,
        fd,
        {
          headers: {
            "Content-Type": "multipart/form-data",
          },
        }
      );
      console.log(result.picture_link);
      if (Object.keys(result.picture_link).length > 0) {
        this.dialog2 = false;
      }
    },
  },
};
</script>

<style>
</style>