<script>
    import WitchImage from "../assets/witch.png";
    import WitchLaughing from "../assets/witch-laughs.mp3";
    import BackgroundTheme from "../assets/halloween.mp3";
    import { onDestroy } from "svelte";

    export let minPause = 5;
    export let maxPause = 35;

    let audioLaugh;
    let audioTheme;
    let img;
    let button;
    let container;

    let active = false;

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
    let animationScaleMax = 1.1;
    let animationScale;

    let currentDistanceX = 0;
    let currentDistanceY = 0;
    let currentScale = 1;
    let currentScaleX = 1;
    let currentRotation = 0;

    let animation, rotateAnimation, floatingAnimation, laughingAnimation;

    let scheduleTimeout = null;

    const schedulePlay = () => {
        stopSchedule();
        if (active) {
            const pause = Math.random() * (maxPause - minPause) + minPause;
            scheduleTimeout = setTimeout(() => {
                if (audioLaugh) {
                    audioLaugh.play();
                    laughingAnimation = container.animate(
                        [
                            { transform: "rotate(0deg)" },
                            { transform: "rotate(-1deg)" },
                            { transform: "rotate(1deg)" },
                            { transform: "rotate(-1deg)" },
                            { transform: "rotate(1deg)" },
                            { transform: "rotate(-2deg)" },
                            { transform: "rotate(2deg)" },
                            {
                                transform: "rotate(-2deg)",
                            },
                            { transform: "rotate(2deg)" },
                            { transform: "rotate(-2deg)" },
                            { transform: "rotate(2deg)" },
                            { transform: "rotate(-2deg)" },
                            { transform: "rotate(1deg)" },
                            { transform: "rotate(-1deg)" },
                            { transform: "rotate(1deg)" },
                            { transform: "rotate(-1deg)" },
                            { transform: "rotate(1deg)" },
                            { transform: "rotate(-1deg)" },
                            { transform: "rotate(0deg)" },
                        ],
                        {
                            delay: 250,
                            duration: audioLaugh.duration * 1000 - 1200,
                            fill: "forwards",
                        }
                    );
                    laughingAnimation.finished.then(() => {
                        laughingAnimation = null;
                        schedulePlay();
                    });
                }
            }, pause * 1000);
            console.log("Scheduled next play in " + pause + " seconds");
        }
    };

    const stopSchedule = () => {
        if (scheduleTimeout) {
            clearTimeout(scheduleTimeout);
        }
    };

    onDestroy(() => {
        stopSchedule();
    });

    const calculateRotation = (x, y) => {
        const offset = 20;
        let distX = x - currentDistanceX;
        let distY = y - currentDistanceY;

        if (distY < 0) {
            return 0;
        } else if (distY > 0) {
            return 15;
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

    const createTransform = (x, y, scale, scaleX, rotation) => {
        return {
            transform:
                "translate(" +
                x +
                "px, " +
                y +
                "px) scaleX(" +
                scaleX +
                ") scaleY(" +
                scale +
                ") rotate(" +
                rotation +
                "deg)",
        };
    };

    const randomizeAnimation = () => {
        if (img && active) {
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
            rotateAnimation = img.animate(
                [
                    createTransform(
                        currentDistanceX,
                        currentDistanceY,
                        currentScale,
                        currentScaleX,
                        currentRotation
                    ),
                    ,
                    createTransform(
                        currentDistanceX,
                        currentDistanceY,
                        currentScale,
                        calculateScaleX(currentScale, animationDistanceX),
                        rotation
                    ),
                ],
                {
                    duration: directionAnimationDuration * 1000,
                    easing: "ease-in-out",
                    fill: "forwards",
                }
            );

            rotateAnimation.finished.then(() => {
                rotateAnimation = null;

                // Move
                animation = img.animate(
                    [
                        createTransform(
                            currentDistanceX,
                            currentDistanceY,
                            currentScale,
                            calculateScaleX(currentScale, animationDistanceX),
                            rotation
                        ),
                        createTransform(
                            animationDistanceX,
                            animationDistanceY,
                            animationScale,
                            scaleX,
                            rotation
                        ),
                    ],
                    {
                        delay: directionAnimationDuration * 1000,
                        duration: animationDuration * 1000,
                        easing: "ease-in-out",
                        fill: "forwards",
                    }
                );
                animation.finished.then(() => {
                    animation = null;

                    currentDistanceX = animationDistanceX;
                    currentDistanceY = animationDistanceY;
                    currentScale = animationScale;
                    currentScaleX = scaleX;
                    currentRotation = rotation;

                    randomizeAnimation();
                });
            });
        }
    };

    const toggleActive = () => {
        active = !active;
        if (active) {
            randomizeAnimation();
            audioTheme.play();
            schedulePlay();
        } else {
            stopSchedule();
            audioLaugh.pause();
            audioLaugh.currentTime = 0;
            audioTheme.pause();
            laughingAnimation?.cancel();
            laughingAnimation = null;

            rotateAnimation?.cancel();
            rotateAnimation = null;
            animation?.cancel();
            animation = null;
            img.animate(
                [
                    createTransform(
                        currentDistanceX,
                        currentDistanceY,
                        currentScale,
                        currentScaleX,
                        currentRotation
                    ),
                    createTransform(0, 0, 1, 1, 0),
                ],
                {
                    duration: 0,
                    fill: "forwards",
                }
            ).finished.then(() => {
                currentDistanceX = 0;
                currentDistanceY = 0;
                currentScale = 1;
                currentScaleX = 1;
                currentRotation = 0;
            });
        }
    };
</script>

<main>
    <div id="floating">
        <div bind:this={container}>
            <img bind:this={img} src={WitchImage} alt="Hexe" width="30%" />
        </div>
    </div>
    <button bind:this={button} on:click={toggleActive} class:active>
        Klicken zum Starten / Stoppen
    </button>
    <audio bind:this={audioLaugh} src={WitchLaughing} />
    <audio bind:this={audioTheme} src={BackgroundTheme} loop />
</main>

<style>
    #floating {
        animation-name: floating;
        animation-duration: 3s;
        animation-iteration-count: infinite;
        animation-timing-function: ease-in-out;
    }

    @keyframes floating {
        0% {
            transform: translate(0, 0px);
        }
        50% {
            transform: translate(-15px, 15px);
        }
        100% {
            transform: translate(0, -0px);
        }
    }
    img {
        filter: drop-shadow(2px 2px 2px #222);
    }

    button {
        position: absolute;
        top: 0px;
        bottom: 0px;
        left: 0px;
        right: 0px;
        background-color: rgba(0, 0, 0, 0.2);
        text-align: center;
        font-size: xx-large;
        filter: drop-shadow(2px 2px 2px #222);
    }
    button.active {
        background-color: transparent;
        outline: none;
        border: none;
        text-align: center;
        color: transparent;
    }
</style>
