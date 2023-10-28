<script>
    import WitchImage from "../assets/witch.png";
    import WitchLaughing from "../assets/witch-laughs.mp3";

    export let width = 326;
</script>

<img src={WitchImage} alt="Hexe" {width} />
<audio src={WitchLaughing} autoplay />

<style>
    img {
        filter: drop-shadow(5px 5px 5px #222);
    }
</style>
