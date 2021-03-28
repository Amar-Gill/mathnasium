<nav>
    <ul>
        <li>page 1</li>
        <li>page 2</li>
        <li>page 3</li>
    </ul>
</nav>

<style>
    nav {
        border-bottom: solid black 1px;
        position: fixed;
        top: 0;
        height: 3rem;
        width: 100%;
        box-shadow: 0 2px 5px black;
    }

    ul {
        display: flex;
        flex-direction: row;
        list-style: none;
        justify-content: center;
        font-weight: bold;
    }

    li {
        margin-right: 1rem;
    }
</style>