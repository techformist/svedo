<script>
  import ApolloClient from "apollo-boost";
  import { setClient } from "svelte-apollo";
  import Todo from "./Todo.svelte";

  export let name;

  const client = new ApolloClient({
    uri: "https://todos-graphql.herokuapp.com/graphql"
  });

  setClient(client);
</script>

<style>
  h1 {
    color: purple;
  }

  h5 {
    color: grey;
  }

  .centrify {
    text-align: center;
  }
</style>

<div class="centrify">
  <h5>Hello {name}!</h5>
  <Todo />
</div>
