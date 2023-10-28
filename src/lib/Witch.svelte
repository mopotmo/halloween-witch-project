<script>
    import WitchImage from "../assets/witch.png";
    import WitchLaughing from "../assets/witch-laughs.mp3";
    import { onMount } from "svelte";

    export let width = 326;
    export let minPause = 2;
    export let maxPause = 30;

    let audio;

    const schedulePlay = () => {
        const pause = Math.random() * (maxPause - minPause) + minPause;
        setTimeout(() => {
            audio.play();
            schedulePlay();
        }, pause * 1000);
        console.log("Scheduled next play in " + pause + " seconds");
    };
</script>

<img src={WitchImage} alt="Hexe" {width} />
<audio bind:this={audio} src={WitchLaughing} autoplay on:ended={schedulePlay} />

<style>
    img {
        filter: drop-shadow(8px 8px 8px #222);
    }
</style>
