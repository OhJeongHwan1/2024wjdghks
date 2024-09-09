<template>
  <div class="modal">
    <div class="clickArea" v-on:click="closeModal"></div>
    <div class="inner">
      <h5 class="mb-10">위치 검색하기</h5>
      <hr class="mb-10" />
      <div class="mb-3">
        <input
          type="text"
          class="form-control"
          placeholder="주소를 검색해 주세요."
          @keyup.enter="search"
          v-model="address"
        />
      </div>
      <div id="map" class="mb-3" style="width: 100%; height: 300px"></div>
      <div class="buttonWrap d-flex gap-2">
        <button class="btn btn-sm btn-success" v-on:click="setPos">
          설정하기
        </button>
        <button class="btn btn-sm btn-danger" v-on:click="closeModal">
          닫기
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from "vue";

let map;
let geocoder;
let marker;
const latitude = ref(0);
const longitude = ref(0);
const address = ref("");
const emit = defineEmits(["closeModal", "setPos"]);
const props = defineProps({
  isModal: Boolean,
});

onMounted(() => {
  if (!("geolocation" in navigator)) {
    return;
  }

  navigator.geolocation.getCurrentPosition(
    (pos) => {
      latitude.value = pos.coords.latitude;
      longitude.value = pos.coords.longitude;

      initMap();
    },
    (err) => {
      alert(err.message);
    }
  );
});

const initMap = () => {
  const container = document.getElementById("map");
  const options = {
    center: new kakao.maps.LatLng(latitude.value, longitude.value),
    level: 3,
  };
  map = new kakao.maps.Map(container, options);
  geocoder = new kakao.maps.services.Geocoder();

  marker = new kakao.maps.Marker({
    position: new kakao.maps.LatLng(latitude.value, longitude.value),
  });

  marker.setMap(map);

  kakao.maps.event.addListener(map, "click", function (mouseEvent) {
    const latlng = mouseEvent.latLng;
    latitude.value = latlng.getLat();
    longitude.value = latlng.getLng();
    marker.setPosition(new kakao.maps.LatLng(latitude.value, longitude.value));
  });
};

function closeModal() {
  emit("closeModal");
}

function setPos() {
  emit("setPos", { latitude: latitude.value, longitude: longitude.value });
}

function search() {
  geocoder.addressSearch(address.value, function (result, status) {
    if (status === kakao.maps.services.Status.OK) {
      latitude.value = result[0].y;
      longitude.value = result[0].x;
      marker.setPosition(
        new kakao.maps.LatLng(latitude.value, longitude.value)
      );
      map.setCenter(new kakao.maps.LatLng(latitude.value, longitude.value));
    }
  });
}
</script>

<style lang="scss" scoped>
.modal {
  position: fixed; // fixed 하기 위해서는 크기 값이 있어야 한다.
  background-color: rgba(0, 0, 0, 0.4);
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 10;
  .inner {
    background: #fff;
    width: 500px;
    padding: 16px 16px;
    border-radius: 5px;
    position: relative;
    z-index: 2;
    @media screen and (max-width: 570px) {
      width: 90%;
    }
  }
  .clickArea {
    width: 100%;
    height: 100%;
    position: absolute;
    z-index: 1;
  }
}
</style>
