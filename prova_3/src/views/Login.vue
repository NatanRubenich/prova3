<template>
    <div>
      <h1>Login</h1>
      <button @click="signInWithGoogle">Sign in with Google</button>
    </div>
  </template>
  
  <script>
  import { getAuth, signInWithPopup, GoogleAuthProvider } from "firebase/auth";
  import { initializeApp } from "firebase/app";
  import { getAnalytics } from "firebase/analytics";
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
  const provider = new GoogleAuthProvider();
  const auth = getAuth();
  
  export default {
    name: "_Login",
    methods: {
      signInWithGoogle() {
        signInWithPopup(auth, provider)
          .then((result) => {
            const user = result.user;
            console.log(user);
            const authStore = useAuthStore();
            authStore.login();
            this.$router.push('/crud');
          })
          .catch((error) => {
            const errorCode = error.code;
            const errorMessage = error.message;
            console.log(errorCode, errorMessage);
          });
      },
    },
  };
  </script>
  