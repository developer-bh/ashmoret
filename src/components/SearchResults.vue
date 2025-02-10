<!-- src/components/SearchResults.vue -->
<template>
  <div class="results-section">
    <div class="uk-container">
      <div class="results">
        <ul uk-accordion>
          <li :class="['card', { multiple: result.SL_CH_Code }]" v-for="result in results" :key="result.id">
            <div class="uk-accordion-title flex">
              <div class="results-percents">
                <span class="percents">
                    <span>
                        <span class="symbol">%</span>
                        {{ result.SL_DiscountTitle1 }}
                    </span>
                    הנחה</span>
              </div>
              <div class="results-logo">
                <div class="results-bage">רשת חנויות</div>
                <img :src="imageUrl(result.SL_LogoName)" :alt="result.SL_Loc_Name"/>
              </div>
              <div class="text">
                <div class="results-title">{{ result.SL_Loc_Name }}</div>
                <div class="results-text">
                  <p>{{ result.SL_LocDescriptionShort }}</p>
                </div>
              </div>
              <div class="results-location"
                   v-if="userLocation() && calculateDistance(result.SL_Longitude, result.SL_Latitude)">
                <span class="icon">
                    <img src="/images/icons/icon-location.svg" alt="Icon"/>
                </span>
                {{ calculateDistance(result.SL_Longitude, result.SL_Latitude).distance }}
                {{
                  calculateDistance(result.SL_Longitude, result.SL_Latitude).measurement === 'meters' ? 'קילומטרים ממך' : 'מטרים ממך'
                }}
              </div>
              <div class="results-icons" v-if="!result.SL_CH_Code">
                <a :href="`tel:${result.SL_BG_Phone}`" class="results-phone">
                                            <span class="icon">
                                                <img src="/images/icons/icon-phone.svg" alt="Phone Icon"/>
                                            </span>
                </a>
                <a :href="wazeUrl(result.SL_Longitude, result.SL_Latitude)" class="results-waze">
                                            <span class="icon">
                                                <img src="/images/icons/icon-waze.svg" alt="Waze Icon"/>
                                            </span>
                </a>
              </div>
              <div class="accordion">
                                    <span class="icon">
                                        <img src="/images/icons/icon-plus-accordion.svg" alt="Plus Icon"/>
                                        <img src="/images/icons/icon-minus-accordion.svg" alt="Minus Icon"/>
                                    </span>
              </div>
            </div>
            <div class="uk-accordion-content">
              <div class="results-additional">
                <div class="flex">
                  <div class="info">
                    <div class="info-flex">
                      <div class="results-big-text">
                        <p>{{ result.SL_LocDescription }}</p>
                      </div>
                      <div class="item">
                        <div class="results-website" v-if="result.SL_Site">
                          <a :href="result.SL_Site" target="_blank" @click="goToSite(result.SL_Site)">
                            <div class="icon">
                              <img src="/images/icons/icon-globe.svg" alt="Icon Globe"/>
                            </div>
                            {{ result.SL_Site }}
                          </a>
                        </div>
                        <div class="results-email" v-if="result.SL_email">
                          <a :href="`mailto:${result.SL_email}`">
                            <div class="icon">
                              <img src="/images/icons/icon-mail.svg" alt="Icon Mail"/>
                            </div>
                            {{ result.SL_email }}
                          </a>
                        </div>
                      </div>
                      <div class="locations-wrapper" v-if="!result.SL_CH_Code">
                        <div class="item">
                          <div class="results-phone" v-if="result.SL_LocPhone">
                            <a :href="`tel:${result.SL_LocPhone}`">
                              <div class="icon">
                                <img src="/images/icons/icon-phone-simple.svg" alt="Icon phone"/>
                              </div>
                              {{ result.SL_LocPhone }}
                            </a>
                          </div>
                          <div class="results-waze">
                            <a :href="wazeUrl(result.SL_Longitude, result.SL_Latitude)" class="results-waze">
                              <div class="icon">
                                <img src="/images/icons/icon-waze-simple.svg" alt="Waze Icon"/>
                              </div>
                              {{ result.SL_AddressLine }}
                            </a>
                          </div>
                        </div>
                      </div>
                      <div class="locations-wrapper" v-else>
                        <div class="item" v-for="storeLocation in result.storeLocations"
                             :key="storeLocation.SL_BG_number" @click="changeMapLocation(storeLocation)">
                          <div class="results-phone" v-if="storeLocation.SL_LocPhone">
                            <a :href="`tel:${storeLocation.SL_LocPhone}`">
                              <div class="icon">
                                <img src="/images/icons/icon-phone-simple.svg" alt="Phone Icon"/>
                              </div>
                              {{ storeLocation.SL_LocPhone }}
                            </a>
                          </div>
                          <div class="results-waze">
                            <a :href="wazeUrl(storeLocation.SL_Longitude, storeLocation.SL_Latitude)"
                               class="results-waze">
                              <div class="icon">
                                <img src="/images/icons/icon-waze-simple.svg" alt="Waze Icon"/>
                              </div>
                              {{ storeLocation.SL_AddressLine }}
                            </a>
                          </div>
                        </div>
                      </div>
                    </div>
                    <div class="results-map">
                      <iframe :src="mapUrl(result.SL_Longitude, result.SL_Latitude)" width="600" height="450"
                              style="border:0;" allowfullscreen="" loading="lazy"
                              referrerpolicy="no-referrer-when-downgrade"></iframe>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    results: {
      type: Array,
      required: true
    },
    storeLocations: {
      type: Array,
      required: false
    }
  },
  methods: {
    imageUrl(logoName) {
      if (!logoName) {
        return '/images/logos/logo-default.png';
      }
      return `http://www.countdown.tempurl.co.il/app/logo/${logoName}`;
    },
    userLocation() {
      const userCoordinates = localStorage.getItem("userCoordinates");
      return !!userCoordinates;
    },
    calculateDistance(locLng, locLat) {
      const userCoordinates = localStorage.getItem("userCoordinates");
      if (!userCoordinates || locLng == null || locLat == null) return null;

      let {latitude: userLat, longitude: userLng} = JSON.parse(userCoordinates);
      userLat = parseFloat(userLat);
      userLng = parseFloat(userLng);
      locLng = parseFloat(locLng);
      locLat = parseFloat(locLat);
      const toRad = (value) => (value * Math.PI) / 180;

      const R = 6371; // Radius of the earth in km
      const dLat = toRad(locLat - userLat);
      const dLng = toRad(locLng - userLng);

      const a =
          Math.sin(dLat / 2) * Math.sin(dLat / 2) +
          Math.cos(toRad(userLat)) * Math.cos(toRad(locLat)) *
          Math.sin(dLng / 2) * Math.sin(dLng / 2);
      const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
      const distance = (R * c * 1000).toFixed(1); // Convert to meters and round to 2 decimal places

      if (isNaN(distance)) {
        return false;
      }

      const result = distance > 900
          ? {distance: Math.round(distance / 1000).toFixed(0), measurement: 'kilometers'}
          : {distance: Math.round(distance), measurement: 'meters'};

      return result;
    },
    wazeUrl(longitude, latitude) {
      return `https://www.waze.com/ul?ll=${latitude},${longitude}&navigate=yes`;
    },
    mapUrl(longitude, latitude) {
      return `https://www.google.com/maps/embed/v1/view?key=YOUR_API_KEY&center=${latitude},${longitude}&zoom=14&maptype=roadmap`;
    },
    goToSite(url) {
      window.open('https://' + url, "_blank");
    },
    changeMapLocation(storeLocation) {
      const {SL_Longitude, SL_Latitude} = storeLocation;
      const mapUrl = this.mapUrl(SL_Longitude, SL_Latitude);
      const mapFrame = this.$el.parentElement.querySelector('.results-additional iframe');
      mapFrame.src = `${mapUrl}&timestamp=${new Date().getTime()}`;
    }
  }
};
</script>

<style scoped>
/* Add any styles if needed */
</style>
