<template>
  <q-page padding>

    <div class="q-pa-none">

      <div class="row">
        <div class="col-sm-7 col-md-8">

          <q-card flat>
            <q-card-section :class="$q.platform.is.mobile ? 'q-px-none' : ''">
              <div class="row q-gutter-xs q-mt-xs justify-between">
                <div class="col-auto">
                  <q-btn-group>
                    <q-btn
                      @click="openEpgSettings(null)"
                      color="primary"
                      icon-right="add"
                      label="Add EPG" />
                  </q-btn-group>
                </div>
              </div>
            </q-card-section>

            <q-card-section :class="$q.platform.is.mobile ? 'q-px-none' : ''">
              <div>
                <q-list
                  bordered
                  separator
                  class="rounded-borders">

                  <q-item
                    v-for="(epg, index) in listOfEpgs"
                    v-bind:key="index"
                    :class="epg.enabled ? '' : 'bg-grey-3'">
                    <q-item-section avatar>
                      <q-icon name="calendar_month" />
                      <!--                  <q-img src="playlist_play"/>-->
                    </q-item-section>

                    <q-item-section top>
                      <q-item-label>{{ epg.name }}</q-item-label>
                      <q-item-label caption lines="3">
                        <span v-html="epg.description"></span>
                      </q-item-label>
                    </q-item-section>

                    <q-item-section top>
                      <q-item-label lines="1">
                        <span class="text-grey-8">{{ epg.url }}</span>
                      </q-item-label>
                    </q-item-section>

                    <q-separator inset vertical class="q-mx-sm" />

                    <q-item-section center side>
                      <div class="text-grey-8 q-gutter-xs">

                        <q-btn-dropdown
                          flat dense rounded
                          size="12px"
                          no-icon-animation
                          dropdown-icon="more_vert">
                          <q-list>

                            <q-item clickable v-close-popup @click="updateEpg(epg.id)">
                              <q-item-section avatar>
                                <q-icon color="info" name="update" />
                              </q-item-section>
                              <q-item-section>
                                <q-item-label>Update</q-item-label>
                              </q-item-section>
                            </q-item>

                            <q-item clickable v-close-popup @click="openEpgSettings(epg.id)">
                              <q-item-section avatar>
                                <q-icon color="grey-8" name="tune" />
                              </q-item-section>
                              <q-item-section>
                                <q-item-label>Configure</q-item-label>
                              </q-item-section>
                            </q-item>

                            <q-item clickable v-close-popup @click="deleteEpg(epg.id)">
                              <q-item-section avatar>
                                <q-icon color="negative" name="delete" />
                              </q-item-section>
                              <q-item-section>
                                <q-item-label>Delete</q-item-label>
                              </q-item-section>
                            </q-item>

                          </q-list>
                        </q-btn-dropdown>

                      </div>
                    </q-item-section>
                  </q-item>

                </q-list>
              </div>
            </q-card-section>
          </q-card>

          <q-card flat>
            <q-card-section :class="$q.platform.is.mobile ? 'q-px-none' : ''">

              <q-form @submit="save" class="q-gutter-md">

                <h5 class="text-primary q-mt-none q-mb-none">Additional EPG Metadata</h5>

                <div class="q-mt-sm">
                  <q-item tag="label" dense class="q-pl-none q-mr-none">
                    <q-item-section avatar>
                      <q-checkbox v-model="enableTmdbMetadata" val="createClientUser" />
                    </q-item-section>
                    <q-item-section>
                      <q-item-label>Fetch missing data from TMDB</q-item-label>
                    </q-item-section>
                  </q-item>
                </div>

                <div
                  v-if="enableTmdbMetadata"
                  class="sub-setting">
                  <q-skeleton
                    v-if="tmdbApiKey === null"
                    type="QInput" />
                  <q-input
                    v-else
                    v-model="tmdbApiKey"
                    label="Your TMDB account API key"
                    hint="Can be found at 'https://www.themoviedb.org/settings/api'."
                  />
                </div>

                <q-separator />

                <div class=" q-mt-sm">
                  <q-item tag="label" dense class="q-pl-none q-mr-none">
                    <q-item-section avatar>
                      <q-checkbox v-model="enableGoogleImageSearchMetadata" val="createClientUser" />
                    </q-item-section>
                    <q-item-section>
                      <q-item-label>Attempt to fetch missing programme images from Google Image Search</q-item-label>
                      <q-item-label caption>This will only fetch the first google image search result for the programme
                        title.
                        It will only be done if TMDB did not find anything.
                      </q-item-label>
                    </q-item-section>
                  </q-item>
                </div>

                <div>
                  <q-btn label="Save" type="submit" color="primary" class="q-mt-lg" />
                </div>

              </q-form>
            </q-card-section>
          </q-card>
        </div>
        <div class="col-sm-5 col-md-4">
          <q-card class="note-card q-my-md">
            <q-card-section>
              <div class="text-h5 q-mb-none">Setup Steps:</div>
              <q-list>

                <q-separator inset spaced />

                <q-item>
                  <q-item-section>
                    <q-item-label>
                      1. Add one or more program guides. Configure these EPGs with a name and URL.
                      URLs can be gzip compressed (.xml.gz) or uncompressed (.xml).
                    </q-item-label>
                  </q-item-section>
                </q-item>
                <q-item>
                  <q-item-section>
                    <q-item-label>
                      2. Click on the kebab menu for each added EPG and click on the <b>Update</b> button to fetch
                      the EPG and import it into TIC's database.
                    </q-item-label>
                  </q-item-section>
                </q-item>

              </q-list>
            </q-card-section>
            <q-card-section>
              <div class="text-h5 q-mb-none">Notes:</div>
              <q-list>

                <q-separator inset spaced />

                <q-item-label class="text-primary">
                  Fetch missing data from TMDB:
                </q-item-label>
                <q-item>
                  <q-item-section>
                    <q-item-label>
                      Configuring the background EPG builder to fetch missing data from TMDB will add significant time
                      to the process.
                      Everytime the programme guide is updated, this background EPG builder process will have to
                      re-fetch any images.
                    </q-item-label>
                  </q-item-section>
                </q-item>

                <q-separator inset spaced />

                <q-item-label class="text-primary">
                  Attempt to fetch missing programme images from Google Image Search:
                </q-item-label>
                <q-item>
                  <q-item-section>
                    <q-item-label>
                      This will cause a lot of google image searches.
                      It is highly likely that you will flag your IP as a bot source with google if you enable this.
                    </q-item-label>
                  </q-item-section>
                </q-item>

              </q-list>
            </q-card-section>
          </q-card>
        </div>
      </div>


    </div>

  </q-page>
</template>

<script>
import {defineComponent, ref} from 'vue';
import axios from 'axios';
import EpgInfoDialog from 'components/EpgInfoDialog.vue';

export default defineComponent({
  name: 'EpgsPage',

  setup() {
    return {};
  },
  data() {
    return {
      listOfEpgs: ref([]),
      enableTmdbMetadata: ref(null),
      tmdbApiKey: ref(null),
      enableGoogleImageSearchMetadata: ref(null),
    };
  },
  methods: {
    fetchSettings: function() {
      // Fetch current settings
      axios({
        method: 'get',
        url: '/tic-api/epgs/get',
      }).then((response) => {
        this.listOfEpgs = response.data.data;
      }).catch(() => {
        this.$q.notify({
          color: 'negative',
          position: 'top',
          message: 'Failed to fetch settings',
          icon: 'report_problem',
          actions: [{icon: 'close', color: 'white'}],
        });
      });
      axios({
        method: 'get',
        url: '/tic-api/get-settings',
      }).then((response) => {
        this.enableTmdbMetadata = response.data.data.epgs.enable_tmdb_metadata;
        this.tmdbApiKey = response.data.data.epgs.tmdb_api_key;
        this.enableGoogleImageSearchMetadata = response.data.data.epgs.enable_google_image_search_metadata;
      }).catch(() => {
        this.$q.notify({
          color: 'negative',
          position: 'top',
          message: 'Failed to fetch settings',
          icon: 'report_problem',
          actions: [{icon: 'close', color: 'white'}],
        });
      });
    },
    openEpgSettings: function(epgId) {
      if (!epgId) {
        epgId = null;
      }
      // Display the dialog
      this.$q.dialog({
        component: EpgInfoDialog,
        componentProps: {
          epgId: epgId,
        },
      }).onOk((payload) => {
        this.fetchSettings();
      }).onDismiss(() => {
      });
    },
    updateEpg: function(epgId) {
      // Fetch current settings
      this.$q.loading.show();
      axios({
        method: 'POST',
        url: '/tic-api/epgs/update/' + epgId,
      }).then((response) => {
        this.$q.loading.hide();
        this.$q.notify({
          color: 'positive',
          icon: 'cloud_done',
          message: 'EPG update queued',
          timeout: 200,
        });
      }).catch(() => {
        this.$q.loading.hide();
        this.$q.notify({
          color: 'negative',
          position: 'top',
          message: 'Failed to queue EPG update',
          icon: 'report_problem',
          actions: [{icon: 'close', color: 'white'}],
        });
      });
    },
    deleteEpg: function(epgId) {
      // Fetch current settings
      this.$q.loading.show();
      axios({
        method: 'DELETE',
        url: `/tic-api/epgs/settings/${epgId}/delete`,
      }).then((response) => {
        this.$q.loading.hide();
        this.fetchSettings();
        this.$q.notify({
          color: 'positive',
          icon: 'cloud_done',
          message: 'EPG deleted',
          timeout: 200,
        });
      }).catch(() => {
        this.$q.loading.hide();
        this.$q.notify({
          color: 'negative',
          position: 'top',
          message: 'Failed to delete EPG',
          icon: 'report_problem',
          actions: [{icon: 'close', color: 'white'}],
        });
      });
    },
    save: function() {
      // Save settings
      let postData = {
        settings: {
          epgs: {
            enable_tmdb_metadata: this.enableTmdbMetadata,
            tmdb_api_key: this.tmdbApiKey,
            enable_google_image_search_metadata: this.enableGoogleImageSearchMetadata,
          },
        },
      };
      axios({
        method: 'POST',
        url: '/tic-api/save-settings',
        data: postData,
      }).then((response) => {
        // Save success, show feedback
        this.fetchSettings();
        this.$q.notify({
          color: 'positive',
          icon: 'cloud_done',
          message: 'Saved',
          timeout: 200,
        });
      }).catch(() => {
        this.$q.notify({
          color: 'negative',
          position: 'top',
          message: 'Failed to save settings',
          icon: 'report_problem',
          actions: [{icon: 'close', color: 'white'}],
        });
      });
    },
  },
  created() {
    this.fetchSettings();
  },
});
</script>
