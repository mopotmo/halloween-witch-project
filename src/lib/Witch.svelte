<script>
    import WitchImage from "../assets/witch.png";
    import WitchLaughing from "../assets/witch-laughs.mp3";
    import { onMount, onDestroy } from "svelte";

    export let width = 326;
    export let minPause = 2;
    export let maxPause = 30;

    let audio;

    let img;

    let animationDurationMin = 3;
    let animationDurationMax = 7;
    let animationDuration;
    let directionAnimationDuration = 1;

    let animationDistanceMinX = 200;
    let animationDistanceMaxX = 400;
    let animationDistanceX;

    let animationDistanceMinY = 50;
    let animationDistanceMaxY = 250;
    let animationDistanceY;

    let animationScaleMin = 0.2;
    let animationScaleMax = 1.2;
    let animationScale;

    let currentDistanceX = 0;
    let currentDistanceY = 0;
    let currentScale = 1;
    let currentScaleX = 1;
    let currentRotation = 0;

    let scheduleTimeout = null;
    const schedulePlay = () => {
        stopSchedule();
        const pause = Math.random() * (maxPause - minPause) + minPause;
        scheduleTimeout = setTimeout(() => {
            if (audio) {
                audio.play();
            }
            schedulePlay();
        }, pause * 1000);

        console.log("Scheduled next play in " + pause + " seconds");
    };

    const stopSchedule = () => {
        if (scheduleTimeout) {
            clearTimeout(scheduleTimeout);
        }
    };

    onDestroy(() => {
        console.log("Destroying");
        stopSchedule();
    });

    const calculateRotation = (x, y) => {
        const offset = 20;
        let distX = x - currentDistanceX;
        let distY = y - currentDistanceY;

        if (distY < 0) {
            return 0;
        } else if (distY > 0) {
            return 25;
        } else {
            return offset;
        }

        return offset + 90 * Math.abs(distY / distX);
    };

    const calculateScaleX = (scale, distanceX) => {
        return scale * (distanceX > currentDistanceX ? 1 : -1);
    };

    const nextX = () => {
        let x =
            (Math.random() * (animationDistanceMaxX - animationDistanceMinX) +
                animationDistanceMinX) *
            (Math.random() < 0.5 ? -1 : 1);
        if (
            (x > 0 && currentDistanceX + x > animationDistanceMaxX) ||
            (x < 0 && currentDistanceX + x < -animationDistanceMaxX)
        ) {
            x *= -1;
        }
        return currentDistanceX + x;
    };

    const nextY = () => {
        let y =
            (Math.random() * (animationDistanceMaxY - animationDistanceMinY) +
                animationDistanceMinY) *
            (Math.random() < 0.5 ? -1 : 1);
        if (
            (y > 0 && currentDistanceY + y > animationDistanceMaxY) ||
            (y < 0 && currentDistanceY + y < -animationDistanceMaxY)
        ) {
            y *= -1;
        }
        return currentDistanceY + y;
    };

    const randomizeAnimation = () => {
        if (img) {
            animationDuration =
                Math.random() * (animationDurationMax - animationDurationMin) +
                animationDurationMin;
            animationDistanceX = nextX();
            animationDistanceY = nextY();
            animationScale =
                Math.random() * (animationScaleMax - animationScaleMin) +
                animationScaleMin;

            let rotation = calculateRotation(
                animationDistanceX,
                animationDistanceY
            );

            let scaleX = calculateScaleX(animationScale, animationDistanceX);

            // Set direction
            img.animate(
                [
                    {
                        transform:
                            "translate(" +
                            currentDistanceX +
                            "px, " +
                            currentDistanceY +
                            "px) scaleX(" +
                            currentScaleX +
                            ") scaleY(" +
                            currentScale +
                            ") rotate(" +
                            currentRotation +
                            "deg)",
                    },
                    {
                        transform:
                            "translate(" +
                            currentDistanceX +
                            "px, " +
                            currentDistanceY +
                            "px) scaleX(" +
                            calculateScaleX(currentScale, animationDistanceX) +
                            ") scaleY(" +
                            currentScale +
                            ") rotate(" +
                            rotation +
                            "deg)",
                    },
                ],
                {
                    duration: directionAnimationDuration * 1000,
                    easing: "ease-in-out",
                    fill: "forwards",
                }
            );

            // Move
            img.animate(
                [
                    {
                        transform:
                            "translate(" +
                            currentDistanceX +
                            "px, " +
                            currentDistanceY +
                            "px) scaleX(" +
                            calculateScaleX(currentScale, animationDistanceX) +
                            ") scaleY(" +
                            currentScale +
                            ") rotate(" +
                            rotation +
                            "deg)",
                    },
                    {
                        transform:
                            "translate(" +
                            animationDistanceX +
                            "px, " +
                            animationDistanceY +
                            "px) scaleX(" +
                            scaleX +
                            ") scaleY(" +
                            animationScale +
                            ") rotate(" +
                            rotation +
                            "deg)",
                    },
                ],
                {
                    delay: directionAnimationDuration * 1000,
                    duration: animationDuration * 1000,
                    easing: "ease-in-out",
                    fill: "forwards",
                }
            );

            currentDistanceX = animationDistanceX;
            currentDistanceY = animationDistanceY;
            currentScale = animationScale;
            currentScaleX = scaleX;
            currentRotation = rotation;

            setTimeout(() => {
                randomizeAnimation();
            }, (directionAnimationDuration + animationDuration) * 1000);
        }
    };

    onMount(() => {
        randomizeAnimation();
        schedulePlay();
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
