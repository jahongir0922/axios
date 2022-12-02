<template>
  <div id="maqola">
    <h1>{{ malumot.nomi }}</h1>
    <!-- <h1>{{ $route.params.id }}</h1> -->
    <p>{{ malumot.matni }}</p>
    <div
      id="tahrircomment"
      @click.self="
        () => {
          modal.style.display = 'none';
        }
      "
      ref="modal"
    >
      <form id="commenttahrirform" @submit.prevent="taxrirlashComment">
        <input type="text" v-model="kimdanQiymat" />
        <textarea
          v-model="commentQiymat"
          name=""
          id=""
          cols="30"
          rows="10"
        ></textarea>
        <input type="submit" value="Tahrirlash" />
      </form>
    </div>
    <div id="comment">
      <form id="postcomment" @submit.prevent="postComment()">
        <input type="text" v-model="kimdanQiymat" placeholder="Ismingiz" />
        <textarea
          v-model="commentQiymat"
          name=""
          id=""
          cols="30"
          rows="10"
          placeholder="izoh qoldiring"
        ></textarea>
        <input type="submit" value="Izoh qoldirish" />
      </form>
      <div
        id="commentblock"
        v-for="(item, index) in comments"
        :key="index"
        class="comment-block"
      >
        <div>
          <h3>{{ item.kimdan }}</h3>
          <button @click="delComment(item.id)">O'chirish</button>
          <button @click="tahrirComment(item.id)">Tahrirlash</button>
        </div>
        <p>{{ item.comment }}</p>
      </div>
      <h1>{{ xatolikpostcomment }}</h1>
    </div>
  </div>
</template>

<script setup>
// import { useRouter } from "vue-router";

import axios from "axios";
import { onMounted, ref } from "vue";
import { useRoute } from "vue-router";
const route = useRoute();
// const router = useRouter();
// console.log(router.params.id);
const malumot = ref({});
const xatolik = ref("");
var commentQiymat = ref();
var kimdanQiymat = ref();
const comments = ref([]);
const xatolikComments = ref("");
const xatolikpostcomment = ref("");
const xatolikTahrircomment = ref("");

const modal = ref(null);
var ayniCommentId = ref(null);
// tahrirash uchun
function tahrirComment(iddagisi) {
  ayniCommentId.value = iddagisi;
  let com = comments.value.filter((item) => {
    return iddagisi === item.id;
  })[0];
  kimdanQiymat.value = com.kimdan;
  commentQiymat.value = com.comment;
  modal.value.style.display = "flex";
}
function taxrirlashComment() {
  axios
    .patch("http://localhost:3000/maqolalarcomment/" + ayniCommentId.value, {
      maqola: route.params.id,
      kimdan: kimdanQiymat.value,
      comment: commentQiymat.value,
    })
    .then(() => {
      kimdanQiymat.value = "";
      commentQiymat.value = "";
      getComment();
      modal.value.style.display = "none";
    })
    .catch((err) => {
      xatolikTahrircomment.value = err.message;
    });
}
// tahrirash uchun

function delComment(iddagisi) {
  axios
    .delete("http://localhost:3000/maqolalarcomment/" + iddagisi)
    .then(() => {
      getComment();
    })
    .catch((err) => {
      xatolikComments.value = err.message;
    })
    .catch((err) => {
      xatolik.value = err.message;
    });
}

function getmaqolalarmalumot() {
  axios
    .get("http://localhost:3000/maqolalarmalumot")
    .then((res) => {
      let ozlashtirgich = res.data.filter((item) => {
        return item.id === route.params.id;
      });
      malumot.value = { ...ozlashtirgich[0] };
    })
    .catch((err) => {
      xatolik.value = err.message;
    });
}
onMounted(() => {
  getmaqolalarmalumot();
  getComment();
});
function getComment() {
  axios
    .get("http://localhost:3000/maqolalarcomment")
    .then((res) => {
      comments.value = [
        ...res.data.filter((item) => {
          return item.maqola === route.params.id;
        }),
        // .reverse(),
      ];
    })
    .catch((err) => {
      xatolikComments.value = err.message;
    })
    .catch((err) => {
      xatolik.value = err.message;
    });
}
async function postComment() {
  axios
    .post("http://localhost:3000/maqolalarcomment", {
      maqola: route.params.id,
      kimdan: kimdanQiymat.value,
      comment: commentQiymat.value,
    })
    .then((res) => {
      kimdanQiymat.value = "";
      commentQiymat.value = "";
      // getComment();
      comments.value.push(res.data);
    })
    .catch((err) => {
      xatolikpostcomment.value = err.message;
    });
}
</script>

<style lang="scss" scoped>
#maqola {
  padding: 20px;
  h1 {
    padding: 10px 0;
  }
  p {
    text-align: justify;
  }
  #tahrircomment {
    display: none;
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    z-index: 999;
    align-items: center;
    justify-content: center;

    background-color: rgba($color: #000000, $alpha: 0.6);

    #commenttahrirform {
      display: flex;
      width: 70%;
      gap: 20px;
      padding: 50px;
      border-radius: 10px;
      background-color: #f1f3f4;
      flex-direction: column;

      input {
        border-radius: 5px;

        height: 40px;
        border: none;
        padding: 10px;
        &:focus {
          outline: none;
        }
      }
      textarea {
        border: none;
        border-radius: 5px;
        padding: 10px;
        &:focus {
          outline: none;
        }
      }
      input[type="submit"] {
        width: 300px;
        background-color: rgb(52, 43, 43);
        color: white;
        &:hover {
          background-color: #000;
        }
      }
    }
  }
  #comment {
    display: flex;
    flex-direction: column;
    gap: 20px;

    #postcomment {
      display: flex;
      flex-direction: column;
      background-color: #f1f3f4;
      gap: 20px;
      padding: 20px;
      border-radius: 5px;

      input {
        border-radius: 5px;

        height: 40px;
        border: none;
        padding: 10px;
        &:focus {
          outline: none;
        }
      }
      textarea {
        border: none;
        border-radius: 5px;

        padding: 10px;
        &:focus {
          outline: none;
        }
      }
      input[type="submit"] {
        width: 300px;
        background-color: rgb(52, 43, 43);
        color: white;
        &:hover {
          background-color: #000;
        }
      }
    }
    #commentblock {
      background-color: #f1f3f4;
      padding: 20px;
      border-radius: 5px;

      div {
        display: flex;
        gap: 10px;
        padding: 5px;
        align-items: center;
        button {
          border-radius: 5px;
          padding: 10px;
          background-color: red;
          &:hover {
            background-color: rgb(189, 6, 6);
          }
          &:active {
            background-color: rgb(229, 207, 207);
          }
          &:last-child {
            background-color: yellow;
            &:hover {
              background-color: rgb(160, 160, 13);
            }
            &:active {
              background-color: rgb(229, 207, 207);
            }
          }
        }
      }
      p {
        padding: 10px;
        background-color: #fff;
        border-radius: 5px;
      }
    }
  }
}
</style>
