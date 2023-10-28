<script>
    import WitchImage from "../assets/witch.png";
    import WitchLaughing from "../assets/witch-laughs.mp3";
    import { onMount } from "svelte";

    export let width = 326;
    export let minPause = 2;
    export let maxPause = 30;

    let audio;
    let scheduleIdAudio;

    let img;

    let animationDurationMin = 4;
    let animationDurationMax = 10;
    let animationDuration =
        Math.random() * (animationDurationMax - animationDurationMin) +
        animationDurationMin;

    let animationDistanceMinX = 200;
    let animationDistanceMaxX = 400;
    let animationDistanceX =
        (Math.random() * (animationDistanceMaxX - animationDistanceMinX) +
            animationDistanceMinX) *
        (Math.random() < 0.5 ? -1 : 1);

    let animationDistanceMinY = 50;
    let animationDistanceMaxY = 250;
    let animationDistanceY =
        (Math.random() * (animationDistanceMaxY - animationDistanceMinY) +
            animationDistanceMinY) *
        (Math.random() < 0.5 ? -1 : 1);

    let animationScaleMin = 0.2;
    let animationScaleMax = 1.2;
    let animationScale =
        Math.random() * (animationScaleMax - animationScaleMin) +
        animationScaleMin;

    let currentDistanceX = 0;
    let currentDistanceY = 0;
    let currentScale = 1;

    const schedulePlay = () => {
        if (scheduleIdAudio) {
            clearTimeout(scheduleIdAudio);
        }
        const pause = Math.random() * (maxPause - minPause) + minPause;
        scheduleIdAudio = setTimeout(() => {
            if (audio) {
                audio.play();
            }
            schedulePlay();
        }, pause * 1000);
        console.log("Scheduled next play in " + pause + " seconds");
    };

    const randomizeAnimation = () => {
        if (img) {
            animationDuration =
                Math.random() * (animationDurationMax - animationDurationMin) +
                animationDurationMin;
            animationDistanceX =
                (Math.random() *
                    (animationDistanceMaxX - animationDistanceMinX) +
                    animationDistanceMinX) *
                (Math.random() < 0.5 ? -1 : 1);
            animationDistanceY =
                (Math.random() *
                    (animationDistanceMaxY - animationDistanceMinY) +
                    animationDistanceMinY) *
                (Math.random() < 0.5 ? -1 : 1);
            animationScale =
                Math.random() * (animationScaleMax - animationScaleMin) +
                animationScaleMin;

            img.style.tr;
            img.animate(
                [
                    {
                        transform:
                            "translate(" +
                            currentDistanceX +
                            "px, " +
                            currentDistanceY +
                            "px) scaleX(" +
                            currentScale +
                            ") scaleY(" +
                            currentScale +
                            ")",
                    },
                    {
                        transform:
                            "translate(" +
                            animationDistanceX +
                            "px, " +
                            animationDistanceY +
                            "px) scale(" +
                            animationScale +
                            ")",
                    },
                ],
                {
                    duration: animationDuration * 1000,
                    easing: "ease-in-out",
                    fill: "forwards",
                }
            );

            currentDistanceX = animationDistanceX;
            currentDistanceY = animationDistanceY;
            currentScale = animationScale;

            console.log(
                "Randomized animation: " +
                    animationDuration +
                    "s, " +
                    animationDistanceX +
                    "px, " +
                    animationDistanceY +
                    "px, scale: " +
                    animationScale
            );

            setTimeout(() => {
                randomizeAnimation();
            }, animationDuration * 1000);
        }
    };

    onMount(() => {
        randomizeAnimation();
    });
</script>

<img bind:this={img} src={WitchImage} alt="Hexe" {width} />
<audio bind:this={audio} src={WitchLaughing} autoplay on:ended={schedulePlay} />

<style>
    :root {
        --animation-start-x: 0px;
        --animation-start-y: 0px;
        --animation-start-scale: 1;
        --animation-time: 10s;
        --animation-distance-x: 500px;
        --animation-distance-y: 500px;
        --animation-scale: 0.3;
    }

    img {
        filter: drop-shadow(8px 8px 8px #222);
    }

    img {
        animation-timing-function: ease-in-out;
    }

    @keyframes floating {
        0% {
            transform: translate(
                var(--animation-start-x),
                var(--animation-start-y)
            );
            transform: scale(var(--animation-start-scale));
        }
        100% {
            transform: translate(
                var(--animation-distance-x),
                var(--animation-distance-y)
            );
            transform: scale(var(--animation-scale));
        }
    }
</style>
