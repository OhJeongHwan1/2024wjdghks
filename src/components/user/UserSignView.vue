<template>
  <div>
    <div class="mb-3">
      <input
        type="text"
        class="form-control"
        placeholder="UserId"
        v-model="userData.userid"
      />
    </div>
    <div class="mb-3">
      <input
        type="text"
        class="form-control"
        placeholder="Password"
        v-model="userData.password"
      />
    </div>
    <div class="mb-3">
      <input
        type="text"
        class="form-control"
        placeholder="UserName"
        v-model="userData.username"
      />
    </div>
    <div class="mb-3">
      <input
        type="text"
        class="form-control"
        placeholder="Address"
        v-model="userData.addr"
      />
    </div>
    <div class="row mb-3">
      <div class="col-4">
        <div class="btn btn-success" style="width: 100%" @click="modalOpen">
          검색
        </div>
      </div>
      <div class="col-4">
        <input
          type="text"
          class="form-control"
          placeholder="Latitude"
          v-model="userData.latitude"
        />
      </div>
      <div class="col-4">
        <input
          type="text"
          class="form-control"
          placeholder="Longitude"
          v-model="userData.longitude"
        />
      </div>
    </div>

    <div class="input-group mb-3">
      <input
        type="file"
        class="form-control"
        id="fileform"
        @change="handleImage"
        accept="imgae/*"
      />
      <label class="input-group-text" for="fileform">Upload</label>
    </div>
    <div class="btn btn-primary" @click="addUser">회원가입</div>
  </div>
  <SearchModalView
    v-if="isModal"
    :isModal="isModal"
    @closeModal="modalClose"
    @setPos="setPos"
  />
</template>

<script setup>
import { ref } from "vue";
import { useStore } from "vuex";
import SearchModalView from "./SearchModalView.vue";

const store = useStore();
const isModal = ref(false);
const userData = ref({
  userid: null,
  password: null,
  username: null,
  addr: null,
  image: null,
  latitude: null,
  longitude: null,
});

const clearForm = () => {
  userData.value.userid = null;
  userData.value.password = null;
  userData.value.username = null;
  userData.value.addr = null;
  userData.value.image = null;
  userData.value.latitude = null;
  userData.value.longitude = null;

  const fileInput = document.getElementById("fileform"); // 반인딩된값이 없기 때문에
  if (fileInput) {
    fileInput.value = ""; // 직접 DOM 에 접근하여 초기화
  }
};

const addUser = () => {
  let temp = {
    userid: userData.value.userid,
    password: userData.value.password,
    username: userData.value.username,
    addr: userData.value.addr,
    image: userData.value.image,
    latitude: userData.value.latitude,
    longitude: userData.value.longitude,
  };
  store.commit("addUser", temp);
  clearForm();
};

const handleImage = (e) => {
  const file = e.target.files[0];
  if (file) {
    userData.value.image = URL.createObjectURL(file);
  }
};

function modalOpen() {
  isModal.value = true;
}
function modalClose() {
  isModal.value = false;
}
function setPos(pos) {
  userData.value.latitude = pos.latitude;
  userData.value.longitude = pos.longitude;
  modalClose();
}
</script>

<style lang="scss" scoped></style>
