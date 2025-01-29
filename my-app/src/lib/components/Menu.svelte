<script>
    export let isOpen = false;
</script>

<div class="menu {isOpen ? 'open' : ''}">
    <h2>Menu</h2>
    <p>This is the menu content.</p>
</div>

<style>
    .menu {
        width: 0;
        height: 100%;
        transition: width 0.3s ease-in-out;
        background-color: rgba(0, 0, 0, 0.8);
        color: white;
        overflow: hidden;
    }

    .menu.open {
        width: 400px;
    }
</style>