<script>
    export let post;
    let isAlert = post.id > 3 ? true : false;
  </script>
  
  <article class:alert={isAlert}>
    <h3>{post.title}</h3>
    <p>{post.body}</p>
  </article>
  
  <style>
    article {
      background-color: #dee;
      padding: 0.5em;
      margin-bottom: 1em;
    }
    .alert {
      background-color: coral;
    }
  </style>