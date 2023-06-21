<template>
  <div>
    <h1>CRUD</h1>
    <div v-for="message in messages" :key="message.id" class="message-container">
      <input v-model="message.text" type="text" class="message-input">
      <div class="button-container">
        <button @click="updateMessage(message)" class="update-button">Update</button>
        <button @click="deleteMessage(message.id)" class="delete-button">Delete</button>
      </div>
    </div>
    <hr>
    <div class="new-message-container">
      <input ref="newmessage" placeholder="Adiciona algo..." type="text" class="new-message-input">
      <button @click="addNewMessage" class="add-button">Adicionar Coisa</button>
    </div>
  </div>
</template>
  
  <script>
  import {
    getFirestore,
    onSnapshot,
    collection,
    doc,
    deleteDoc,
    setDoc,
    addDoc,
    orderBy,
    query,
  } from "firebase/firestore";
  import { initializeApp } from "firebase/app";
  import { getAnalytics } from "firebase/analytics";
  import { ref, onUnmounted } from "vue";
  import { useAuthStore } from '@/store';

  const firebaseConfig = {
    apiKey: "AIzaSyBmNLwgZaQUxr6uVlBjjDKaIsSVsd-1Yxk",
    authDomain: "prova3-b24d7.firebaseapp.com",
    projectId: "prova3-b24d7",
    storageBucket: "prova3-b24d7.appspot.com",
    messagingSenderId: "689099991351",
    appId: "1:689099991351:web:f036614a3763f92eaa52b2",
    measurementId: "G-HYDJFRVSL0",
  };
  
  const app = initializeApp(firebaseConfig);
  const analytics = getAnalytics(app);
  console.log(analytics);
  const db = getFirestore(app);
  
  export default {
    name: "_Crud",
    components: {
      
      // eslint-disable-next-line 
      isLoggedIn() {
      const authStore = useAuthStore();
      return authStore.loggedIn;    
    },
  },
    methods: {
      addNewMessage: function () {
        addDoc(collection(db, "messages"), {
          text: this.$refs.newmessage.value,
          date: Date.now(),
        });
      },
      updateMessage: function (message) {
        setDoc(doc(db, "messages", message.id), {
          text: message.text,
          date: message.date,
        });
      },
      deleteMessage: function (id) {
        deleteDoc(doc(db, "messages", id));
      },
    },
    data: () => {
      return {
        messages: ref([]),
      };
    },
    mounted() {
      const latestQuery = query(collection(db, "messages"), orderBy("date"));
      const livemessages = onSnapshot(latestQuery, (snapshot) => {
        this.messages = snapshot.docs.map((doc) => {
          return {
            id: doc.id,
            text: doc.data().text,
            date: doc.data().date,
          };
        });
      });
      onUnmounted(livemessages);
    },
  };
  </script>
  


<style scoped>
h1 {
  font-size: 24px;
  margin-bottom: 20px;
}

.message-container {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}

.message-input {
  flex: 1;
  padding: 5px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.button-container {
  margin-left: 10px;
}

.update-button,
.delete-button {
  background-color: #4285f4;
  color: white;
  padding: 5px 10px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  margin-right: 5px;
}

.update-button:hover,
.delete-button:hover {
  background-color: #3367d6;
}

.new-message-container {
  margin-top: 20px;
}

.new-message-input {
  padding: 5px;
  border: 1px solid #ccc;
  border-radius: 4px;
  width: 200px;
}

.add-button {
  background-color: #4285f4;
  color: white;
  padding: 5px 10px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  margin-left: 10px;
}

.add-button:hover {
  background-color: #3367d6;
}
</style>