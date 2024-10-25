<script>
export let headingTitle;
</script>

<header>
    <h1>{headingTitle}</h1>
    <nav>
        <ul>
            <li><a href="/">Home</a></li>
            <li><a href="about/">About</a></li>
            <li><a href="contact/">Contact</a></li>
            <li><a href="news">News</a></li>
        </ul>
    </nav>
</header>

<style>
     header {
            background-color: #2c3e50;
            display: flex;
            align-items: center;
            padding: 2px 3px;
        }
        header h1 {
            color: white;
            flex-grow: 1;
            text-align: center;
        }
        nav {
            display: flex;
        }
        nav ul {
            display: flex;
        }
        nav li {
            list-style: none;
            margin: 0 15px;
        }
        nav a {
            color: white;
            text-decoration: none;
            padding: 10px;
            transition: background-color 0.3s;
        }
        nav a:hover {
            background-color: #34495e;
            border-radius: 50px;
        }
</style>