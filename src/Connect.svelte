<script>
  import { Snackbar, Dialog, Input, Field, Button, Icon } from "svelma";
  import { onMount } from "svelte";
  import { firebase } from "./firebase.js";

  export let isGood = false;

  // Firebase config
  let firebaseConfig = {
    apiKey: "",
    authDomain: "",
    databaseURL: "",
    projectId: "",
    storageBucket: ""
  };

  // Firebase
  //let db = firebase.firestore();
  //let upload = firebase.storage();

  // When page is loaded: get data from local storage
  onMount(async () => {
    let obj = localStorage.getItem("firebaseData");
    if (obj !== null) {
      firebaseConfig = JSON.parse(obj);
    }
  });

  let isLoading = false; // for UX
  // Connect To Firebase
  async function connectToFirebase() {
    if (
      firebaseConfig.apiKey === "" ||
      firebaseConfig.authDomain === "" ||
      firebaseConfig.databaseURL === "" ||
      firebaseConfig.projectId === "" ||
      firebaseConfig.storageBucket === ""
    ) {
      Dialog.confirm({
        message: "Cannot save empty data!",
        title: "Empty Data",
        type: "is-danger",
        icon: "times-circle"
      });
      return;
    }

    // Because firebase init one time (globaly)
    if (!firebase.apps.length) {
      firebase.initializeApp(firebaseConfig);
    }

    // Send a small data to the firebase: afor testing
    try {
      isLoading = true;
      await firebase
        .firestore()
        .collection("/connection")
        .doc("just_for_connection_test")
        .set({
          test: "zaki_connection_test"
        });

      localStorage.setItem("firebaseData", JSON.stringify(firebaseConfig));
      isLoading = false;
      Snackbar.create({
        message: `Suucessful Connection`,
        background: "has-background-success",
        type: "is-white"
      });

      isGood = true;
    } catch (error) {
      Snackbar.create({
        message: `${error}`,
        background: "has-background-danger",
        type: "is-white"
      });
      isLoading = false;
    }
  }
</script>

<svelte:head>
  <style>
    .has-image-centered {
      margin-left: auto;
      margin-right: auto;
    }
    .up {
      margin-top: 5rem;
    }
    #image-logo {
      width: 18rem;
      margin-bottom: 3rem;
    }
    html {
      overflow: hidden;
      background: rgb(124, 27, 214);
      background: linear-gradient(
        144deg,
        rgba(124, 27, 214, 1) 27%,
        rgba(181, 121, 236, 1) 90%
      );
    }
  </style>
</svelte:head>

<div class="up">
  <div class="columns is-centered is-vcentered">
    <div class="column is-7">

      <!-- Quiz Logo -->
      <figure id="image-logo" class="image has-image-centered">
        <img src="/quiz_img.png" alt="quiz logo image" />
      </figure>
      <div class="box">
        <!-- Firabase Image Logo -->
        <figure class="image is-128x128 has-image-centered">
          <img src="/firebase.png" alt="firebase logo image" />
        </figure>

        <!-- Field 1 -->
        <Field label="apiKey">
          <Input
            type="text"
            bind:value={firebaseConfig.apiKey}
            placeholder="ASzaSyC5G88TD9EL8Tv4II3-ZAKIriCL5Y8dko"
            size="is-small" />
        </Field>

        <!-- Field 2 -->
        <Field label="authDomain">
          <Input
            type="text"
            bind:value={firebaseConfig.authDomain}
            placeholder="myapp-30a80.firebaseapp.com"
            size="is-small" />
        </Field>

        <!-- Field 3 -->
        <Field label="databaseURL">
          <Input
            type="text"
            bind:value={firebaseConfig.databaseURL}
            placeholder="https://myapp-30a80.firebaseio.com"
            size="is-small" />
        </Field>

        <!-- Field 4 -->
        <Field label="projectId">
          <Input
            type="text"
            bind:value={firebaseConfig.projectId}
            placeholder="myapp-30a80"
            size="is-small" />
        </Field>

        <!-- Field 5 -->
        <Field label="storageBucket">
          <Input
            type="text"
            bind:value={firebaseConfig.storageBucket}
            placeholder="myapp-30a80.appspot.com"
            size="is-small" />
        </Field>

        <!-- Submit Button -->
        <Button
          loading={isLoading}
          on:click={connectToFirebase}
          type="is-warning is-fullwidth is-medium"
          iconPack="fa"
          iconRight="fire">
          Connect!
        </Button>
      </div>
    </div>
  </div>
</div>
