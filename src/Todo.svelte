<script>
  import { getClient, query, mutate } from "svelte-apollo";
  import { gql } from "apollo-boost";

  const GETTODO = gql`
    {
      allTodos {
        nodes {
          id
          title
          done
        }
      }
    }
  `;

  const ADDTODO = gql`
    mutation($todoEdit: String!) {
      createTodo(input: { todo: { title: $todoEdit, done: false } }) {
        todo {
          id
          title
          done
        }
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
      .then(data => {
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

    {#each data.data['allTodos']['nodes'] as todo, i}
      <p class:done={todo.done}>{todo.title}</p>
    {/each}

  {:catch e}
    {e}
  {/await}

</div>
