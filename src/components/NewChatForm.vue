<template>
  <form class="custom-container">
    <input
      class="custom-input"
      placeholder="text message and hit enter to send"
      v-model="message"
      @keypress.enter.prevent="handleSubmit"
    />
    <img
      class="image pointer"
      src="../../public/send.png"
      alt=""
      @click="handleSubmit"
    />
  </form>
</template>

<script>
import { computed, ref } from "vue";
import { serverTimestamp } from "firebase/firestore";
import useCollection from "../composables/useCollection";
import getLoginUser from '@/composables/getLoginUser';
export default {
  props: ["senderId", "receiverId", "senderName", "receiverName", "receiverPhotoUrl"],
  setup(props) {
    let message = ref("");

    let { user } = getLoginUser();
    let sender_id = computed(() => props.senderId);
    let receiver_id = computed(() => props.receiverId);
    let sender_name = computed(() => props.senderName);
    let receiver_name = computed(() => props.receiverName);
    let receiver_photo_url = computed(() => props.receiverPhotoUrl);

    let { saveDoc } = useCollection("messages");
    let handleSubmit = async () => {
      let chat = {
        message: message.value,
        name: user.value.displayName,
        created_at: serverTimestamp(),
        sender_id: sender_id.value,
        receiver_id: receiver_id.value,
        sender_name: sender_name.value,
        receiver_name: receiver_name.value,
        receiver_photo_url: receiver_photo_url.value ? receiver_photo_url.value : "",
        sender_photo_url : user.value.photoURL ? user.value.photoURL : "",
        read_status: false
      };
      await saveDoc(chat);
      message.value = "";
    };

    return { message, handleSubmit };
  },
};
</script>

<style scoped>
form {
  margin: 10px 20px;
}
.custom-input {
  width: 100%;
  max-width: 100%;
  padding: 10px;
  box-sizing: border-box;
  border: 0;
  border-radius: 20px;
  font-family: inherit;
  background-color: #272727;
  padding-right: 55px;
  color: lightgray;
  outline: none;
  height: 50px;
}
.custom-container {
  position: relative;
}
.image {
  position: absolute;
  right: 15px;
  top: 13px;
  width: 25px !important;
  height: 25px !important;
}
</style>
