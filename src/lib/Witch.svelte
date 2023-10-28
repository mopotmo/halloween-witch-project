<script>
    import WitchImage from "../assets/witch.png";

    export let width = 326;
</script>

<img src={WitchImage} alt="Hexe" {width} />

<style>
    img {
        filter: drop-shadow(5px 5px 5px #222);
    }
</style>
