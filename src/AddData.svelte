<script>
  import { Snackbar, Input, Field, Button, Icon } from "svelma";

  // Questions
  let question = "";
  let question_description = "";
  let image = [];

  // Answers
  let text = "";
  let isCorrect = false;
  let answers = [];

  // Preview
  let image_preview = "./no image.png"; // just for preview :p
  $: if (image[0]) {
    // to preview :p
    image_preview = URL.createObjectURL(image[0]);
  }

  // Q&A
  let questions_answers = []; // to be send eventually
  let isLoading = false;

  // ----------- Functions ------------- //

  // add answers
  function addAnswer() {
    let a = { text, isCorrect };
    answers = [...answers, a];
    text = "";
  }
  // add answers
  function deleteAnswer(index) {
    answers.splice(index, 1);
    answers = answers;
  }
  // add questions and answers to the stuck
  function addQ_A() {
    if (answers.length === 0) {
      // for do not adding empty answers (we have to respect the array schema)
      return;
    }
    let qa = {
      text_question: question,
      answers: answers
    };
    questions_answers = [...questions_answers, qa];
    // clear fields
    answers = [];
    question = "";
    text = "";
  }
  // delete questions and answers from the stuck
  function deleteQ_A(index) {
    questions_answers.splice(index, 1);
    questions_answers = questions_answers;
  }

  function sendToFirebase(){

  }
</script>

<nav class="navbar" role="navigation" aria-label="main navigation">
</nav>

<div class="columns is-centered is-vcentered">
  <div class="column is-7">

    <!-- Add question description -->
    <Field label="Add description">
      <Input
        type="textarea"
        maxlength="200"
        bind:value={question_description} />
    </Field>
    <br />

    <!-- Add question -->
    <Field label="Add question">
      <Input type="text" bind:value={question} icon="question-circle" />
    </Field>

    <!-- Add answer -->
    <Field>
      <Input type="text" bind:value={text} icon="list" expanded="true" />
      <div class="control">
        <div class="select">
          <select bind:value={isCorrect}>
            <option value={false}>false</option>
            <option value={true}>true</option>
          </select>
        </div>
      </div>
      <div class="control">
        <Button type="is-success" on:click={addAnswer}>
          <Icon icon="plus" />
        </Button>
      </div>
    </Field>

    <!-- Answers .. -->
    {#each answers as { text, isCorrect }, i}
      {#if answers.lenght !== 0}
        <div class="field has-addons">
          <div class="control">
            <button class="button is-static">{i + 1}</button>
          </div>
          <div class="control is-expanded">
            <input type="text" class="input " value={text} readonly />
          </div>
          <div class="control">
            <input type="text" class="input" value={isCorrect} readonly />
          </div>
          <div class="control">
            <button class="button is-danger" on:click={() => deleteAnswer(i)}>
              <span class="icon">
                <i class="fas fa-trash-alt" />
              </span>
            </button>
          </div>
        </div>
      {/if}
    {/each}

    <!-- validate (add q and a to list) -->
    <Field>
      <Button type="is-success is-fullwidth" on:click={addQ_A}>
        <Icon icon="check" />
      </Button>
    </Field>

    <!-- Show Q&A (stuck :) -->
    <aside class="menu">
      {#each questions_answers as q, i}
        <ul class="menu-list">
          <!-- show question -->
          <li>
            <span class="menu-label">{q.text_question}</span>
            <a class="delete" on:click={() => deleteQ_A(i)} />
          </li>
          <!-- show answers -->
          <li>
            {#each q.answers as a, j}
              <ul>
                <li>
                  <div class="field has-addons">
                    <div class="control">
                      <button class="button is-static is-small">{j + 1}</button>
                    </div>
                    <div class="control is-expanded">
                      <input
                        type="text"
                        class="input is-small"
                        value={a.text}
                        readonly />
                    </div>
                    <div class="control">
                      <button class="button is-static is-small">
                        {#if a.isCorrect}
                          <span class="icon has-text-success">
                            <i class="fas fa-thumbs-up" />
                          </span>
                        {:else}
                          <span class="icon">
                            <i class="fas fa-thumbs-down" />
                          </span>
                        {/if}
                      </button>
                    </div>
                  </div>
                </li>
              </ul>
            {/each}
          </li>
        </ul>
      {:else}
        <div class="notification has-text-centered">
          No Questions and Answers Here ..!
        </div>
      {/each}
    </aside>

    <!-- Upload Image -->
    <label class="label">Add Audio Description</label>
    <div class="file has-name is-fullwidth">
      <label class="file-label">
        <input
          class="file-input"
          type="file"
          bind:files={image}
          accept=".jpg,.png" />
        <span class="file-cta">
          <span class="file-icon">
            <i class="fas fa-upload" />
          </span>
          <span class="file-label">Choose an image</span>
        </span>
        <span class="file-name">
          {#if image && image[0]}{image[0].name}{:else}No image to upload{/if}
        </span>
      </label>
    </div>

    <!-- Image to preview -->
    <figure class="image has-image-centered">
      <img src={image_preview} alt="preview logo image" />
    </figure>

    <!-- Submit Button -->
    <Button
      loading={isLoading}
      on:click={sendToFirebase}
      type="is-success is-fullwidth is-medium"
      iconPack="fa"
      iconRight="fire">
      Send !
    </Button>

  </div>
</div>
