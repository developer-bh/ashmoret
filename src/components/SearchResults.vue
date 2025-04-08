<!-- src/components/SearchResults.vue -->
<template>
  <div class="results-section">
    <div class="uk-container">
      <div class="results">
        <ul uk-accordion>
          <li :class="['card', { multiple: result.SL_CH_Code }]" v-for="result in results" :key="result.SL_BG_number" :data-id="result.SL_BG_number">
            <div class="uk-accordion-title flex" @click="changeMapLocation(result)">
              <div class="flex-item">
                <div class="flex">
                  <div class="results-percents">
                    <span class="percents">
                      <span>
                        <span class="symbol">%</span>
                        {{ result.SL_DiscountTitle1 }}
                      </span>
                      הנחה
                      <span class="discount-info">במעמד החיוב</span>
                    </span>
                  </div>
                  <div class="results-logo">
                    <div class="results-bage">רשת חנויות</div>
                    <SafeImage :src="result.SL_LogoName"/>
                  </div>
                </div>
                <div class="text">
                  <div class="results-title">
                    {{ result.SL_Loc_Name }}
                    <div v-if="result.amountOfChainStore" class="amount-of-chain-store">
                      ( <span v-if="result.amountOfNewChainStores" class="chain-span">NEW</span> {{result.amountOfNewChainStores > 0 ? (result.amountOfNewChainStores + ' + ') : ''}} {{ result.amountOfChainStore }} {{ result.amountOfChainStore === 1 ? ' חנות' : ' חנויות' }}  )
                    </div>
                  </div>
                  <div class="results-text">
                    <p>{{ result.SL_LocDescriptionShort }}</p>
                  </div>
                  <div v-if="result.isPromoted" class="is-promoted">
                  </div>
                  <div class="results-location show-mobile"
                    v-if="userLocation() && calculateDistance(result.SL_Longitude, result.SL_Latitude)">
                    <span class="icon">
                      <img src="/images/icons/icon-location.svg" alt="Icon" />
                    </span>
                    {{ calculateDistance(result.SL_Longitude, result.SL_Latitude).distance }}
                  {{ calculateDistance(result.SL_Longitude, result.SL_Latitude).measurement === 'meters' ? 'מטרים ממך' : (calculateDistance(result.SL_Longitude, result.SL_Latitude).measurement === 'kilometers' ? 'קילומטרים ממך' : 'קילומטר ממך ') }}
                  </div>
                </div>
              </div>
              <div class="flex-item">
                <div class="results-location show-desctop"
                    v-if="userLocation() && calculateDistance(result.SL_Longitude, result.SL_Latitude)">
                  <span class="icon">
                    <img src="/images/icons/icon-location.svg" alt="Icon" />
                  </span>
                  {{ calculateDistance(result.SL_Longitude, result.SL_Latitude).distance }}
                {{ calculateDistance(result.SL_Longitude, result.SL_Latitude).measurement === 'meters' ? 'מטרים ממך' :  : (calculateDistance(result.SL_Longitude, result.SL_Latitude).measurement === 'kilometers' ? 'קילומטרים ממך' : 'קילומטר ממך ') }}
                </div>
                <div class="results-icons" v-if="!result.SL_CH_Code">
                  <a :href="`tel:${result.SL_BG_Phone}`" class="results-phone" @click.stop>
                    <span class="icon">
                      <img src="/images/icons/icon-phone.svg" alt="Phone Icon" />
                    </span>
                  </a>
                  <a :href="wazeUrl(result.SL_Longitude, result.SL_Latitude)"
                     target="_blank"
                     class="results-waze"
                     @click.stop>
                    <span class="icon">
                      <img src="/images/icons/icon-waze.svg" alt="Waze Icon" />
                    </span>
                  </a>
                  
                </div>
                  <div class="accordion show-mobile">
                                      <span class="icon">
                                          <img src="/images/icons/icon-plus-accordion.svg" alt="Plus Icon"/>
                                          <img src="/images/icons/icon-minus-accordion.svg" alt="Minus Icon"/>
                                      </span>
                </div>
              </div>
              <div class="flex-item">
                              <div class="accordion show-desctop">
                                      <span class="icon">
                                          <img src="/images/icons/icon-plus-accordion.svg" alt="Plus Icon"/>
                                          <img src="/images/icons/icon-minus-accordion.svg" alt="Minus Icon"/>
                                      </span>
                </div>
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
                      <div class="item" v-if="result.SL_Site || result.SL_email">
                        <div class="results-website" v-if="result.SL_Site">
                          <a :href="result.SL_Site" target="_blank" @click="goToSite(result.SL_Site)">
                            <div class="icon">
                              <img src="/images/icons/icon-globe.svg" alt="Icon Globe" />
                            </div>
                            {{ result.SL_Site }}
                          </a>
                        </div>
                        <div class="results-email" v-if="result.SL_email">
                          <a :href="`mailto:${result.SL_email}`">
                            <div class="icon">
                              <img src="/images/icons/icon-mail.svg" alt="Icon Mail" />
                            </div>
                            {{ result.SL_email }}
                          </a>
                        </div>
                      </div>
                      <div class="locations-wrapper" v-if="!result.SL_CH_Code">
                        <div class="item">
                          <div class="results-phone" v-if="result.SL_LocPhone">
                            <a :href="`tel:${result.SL_LocPhone}`">
                              {{ result.SL_LocPhone }}
                              <div class="icon">
                                <img src="/images/icons/icon-phone-simple.svg" alt="Icon phone" />
                              </div>
                            </a>
                          </div>
                          <div class="results-waze">
                            <a :href="wazeUrl(result.SL_Longitude, result.SL_Latitude)"
                               target="_blank">
                              {{ result.SL_AddressLine }}
                              <div class="icon">
                                <img src="/images/icons/icon-waze-simple.svg" alt="Waze Icon" />
                              </div>
                            </a>
                          </div>
                        </div>
                      </div>
                      <div class="locations-wrapper" v-else>
                        <div class="item item-location" v-for="storeLocation in result.storeLocations" :key="storeLocation.SL_BG_number" :class="{ 'active': selectedLocation === storeLocation.SL_BG_number }" @click="changeMapLocation(storeLocation)">
                          <div class="results-phone" v-if="storeLocation.SL_LocPhone">
                            <a :href="`tel:${storeLocation.SL_LocPhone}`">
                              {{ storeLocation.SL_LocPhone }}
                              <div class="icon">
                                <img src="/images/icons/icon-phone-simple.svg" alt="Phone Icon" />
                              </div>
                            </a>
                          </div>
                          <div class="results-waze">
                            <span v-if="userLocation() && storeLocation.SL_BG_number === result.SL_BG_number">הסניף הקרוב ביותר</span>
                            <a :href="wazeUrl(storeLocation.SL_Longitude, storeLocation.SL_Latitude)"
                               target="_blank">
                              {{ storeLocation.SL_AddressLine }}
                              <div class="icon">
                                <img src="/images/icons/icon-waze-simple.svg" alt="Waze Icon" />
                              </div>
                            </a>
                          </div>
                        </div>
                      </div>
                    </div>
                    <div class="results-map" v-if="!result.SL_CH_Code">
                      <iframe :src="mapUrl(result.SL_Longitude, result.SL_Latitude)" width="600" height="450" style="border:0;" allowfullscreen="" loading="lazy" referrerpolicy="no-referrer-when-downgrade"></iframe>
                    </div>
                    <div class="results-map" v-else>
                      <iframe :src="mapSrc" width="600" height="450" style="border:0;" allowfullscreen="" loading="lazy" referrerpolicy="no-referrer-when-downgrade"></iframe>
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
import axios from 'axios';
import SafeImage from './SafeImage.vue';

export default {
  components: {SafeImage},
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
  data() {
    return {
      selectedLocation: null,
      mapSrc: null, // Set default map URL
      iframeLoaded: false,
      closestToLocation: null
    };
  },
  methods: {
    userLocation() {
      return this.$isLocationAvailable();
    },
    calculateDistance(locLng, locLat) {
      if (!this.$isLocationAvailable() || locLng == null || locLat == null) return null;

      let { latitude: userLat, longitude: userLng } = this.$getUserLocation();
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
          ? { distance: Math.round(distance / 1000).toFixed(0), measurement: Math.round(distance / 1000) === 1 ? 'kilometer' : 'kilometers' }
          : { distance: Math.round(distance), measurement: 'meters' };

      return result;
    },
    wazeUrl(longitude, latitude) {
      return `https://www.waze.com/ul?ll=${latitude},${longitude}&navigate=yes`;
    },
    mapUrl(longitude, latitude) {
      return `https://www.google.com/maps/embed/v1/place?key=AIzaSyD4StBI8c6oIX-84OPC6W7VVeQBJtxza4Y&q=${latitude},${longitude}&zoom=14&maptype=roadmap`;
    },
    goToSite(url) {
      if (!/^(https?|http):\/\//i.test(url)) {
        url = 'https://' + url;
      }
      window.open(url, "_blank");
    },
    changeMapLocation(storeLocation) {
      this.mapSrc = null;
      const SL_Longitude = storeLocation.SL_Longitude;
      const SL_Latitude = storeLocation.SL_Latitude;

      this.mapSrc = this.mapUrl(SL_Longitude, SL_Latitude);
      this.selectedLocation = storeLocation.SL_BG_number;
    }
  }
};
</script>
