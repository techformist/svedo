<script>
  import { getClient, query, mutate } from "svelte-apollo";
  import { gql } from "apollo-boost";

  const GETTODO = gql`
    {
      getTodos {
        id
        text
        done
      }
    }
  `;

  const ADDTODO = gql`
    mutation($todoEdit: String!) {
      addTodo(text: $todoEdit) {
        id
        text
        done
      }
    }
  `;

  let todoEdit = "";
  const client = getClient();
  const todoOp = query(client, { query: GETTODO });

  function addTodo() {
    const todoAdd = mutate(client, {
      mutation: ADDTODO,
      variables: {
        todoEdit
      }
    })
      .then(() => {
        todoEdit = "";
        todoOp.refetch();
      })
      .catch(e => {
        console.error("error: ", e);
      });
  }
</script>

<style>
  .done {
    text-decoration: line-through;
  }
</style>

<div style="text-align:center">

  <h2>Svedos</h2>
  <form on:submit|preventDefault={addTodo}>
    <input placeholder="new todo" bind:value={todoEdit} />
    <button method="submit">Submit</button>
  </form>

  {#await $todoOp}
    <p>.. loading</p>
  {:then data}

    <ul>
      {#each data.data['getTodos'] as todo, i}
        <li class:done={todo.done}>{todo.text}</li>
      {/each}
    </ul>
  {:catch e}
    {e}
  {/await}

</div>
