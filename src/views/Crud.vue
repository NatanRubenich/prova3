<template>
    <div>
      <h1>CRUD</h1>
      <div v-for="message in messages" :key="message.id">
        <input v-model="message.text" type="text">
        <button @click="updateMessage(message)">update</button>
        <button @click="deleteMessage(message.id)">delete</button>
      </div>
      <hr>
      <input ref="newmessage" placeholder="new message ..." type="text">
      <button @click="addNewMessage">addnew</button>
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
  