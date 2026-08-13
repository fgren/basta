<script lang="ts">
    import { onMount } from "svelte";
    import { page } from "$app/stores";
    import { fade } from "svelte/transition";

    let time = new Date();

    let config = {
        split: true,
        numbers: true,
        show_inactive_numbers: false,
        winter: 0,
        santa: false,
        true_binary_seconds: false,
        info: "2",
    };

    const to_bin = (num: number, pad: number = 6) =>
        num.toString(2).padStart(pad, "0");

    const weekdays = ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"];

    $: hours = to_bin(time.getHours(), 5).padStart(6, "-");
    $: minutes = to_bin(time.getMinutes());
    // get 64:th of a second seconds version: Math.round((time.getSeconds() * time.getMilliseconds()) / 937.5)
    $: seconds = to_bin(
        config.true_binary_seconds
            ? Math.floor(
                  (time.getSeconds() + time.getMilliseconds() / 1000) / 0.9375,
              )
            : time.getSeconds(),
    );

    onMount(() => {
        config = {
            split: !$page.url.searchParams.has("split"),
            numbers: !$page.url.searchParams.has("numbers"),
            show_inactive_numbers: $page.url.searchParams.has(
                "show_inactive_numbers",
            ),
            winter: Number.parseInt(
                $page.url.searchParams.get("winter") ?? "0",
            ),
            santa: $page.url.searchParams.has("santa"),
            true_binary_seconds: $page.url.searchParams.has(
                "true_binary_seconds",
            ),
            info: $page.url.searchParams.get("info") ?? "",
        };

        let handle: string | number | NodeJS.Timer | undefined;
        if (config.true_binary_seconds) {
            handle = setInterval(() => (time = new Date()), 937.5);
        } else {
            handle = setInterval(() => (time = new Date()), 1000);
        }

        return () => clearInterval(handle);
    });

    let odd_heart: boolean;
    $: odd_heart = hours.at(-1) === "1";

    const random_snowflake = () => `&#1005${Math.floor(Math.random() * 3 + 2)}`;

    /**
     * Returns amount of degrees rotated since midnight for h, m, s
     * @param now current time for rotations
     */
    const analog_rotate = (now: Date) => {
        const midnight = new Date(
            now.getFullYear(),
            now.getMonth(),
            now.getDate(),
            0,
            0,
            0,
        );
        const mil_delta = now.getTime() - midnight.getTime();
        // time since midnight
        const h = mil_delta / (1000 * 60 * 60);
        const m = h * 60;
        const s = m * 60;
        // convert time to rotations since midnight
        return {
            h: Math.round(h * 30 + h / 2),
            m: Math.round(m * 6),
            s: Math.round(s * 6),
        };
    };
</script>

{#if config.winter > 0}
    {#each Array(config.winter) as i}
        <div class="snow">{@html random_snowflake()}</div>
    {/each}
{/if}

<div class="container">
    {#each [hours, minutes, seconds] as row, i}
        <div class="row">
            {#each row.split("") as unit, j}
                {@const secondary =
                    config.split && !(i % 2 == 0 ? j % 2 == 0 : j % 2 == 1)}
                {#if unit === "-"}
                    <div
                        class="circle show_inactive_numbers"
                        style:visibility={"hidden"}
                        class:clock={config.info === "2"}
                        class:active={odd_heart}
                        class:secondary
                    ></div>
                {:else}
                    {@const active = Boolean(Number.parseInt(unit))}

                    <svg class="circle" class:active class:secondary
                        id="Layer_1"
                        data-name="Layer 1"
                        xmlns="http://www.w3.org/2000/svg"
                        viewBox="0 0 1134.3395 786.7908"
                        ><defs
                            ><style>
                                .Cheps_Color {
                                    fill: #231f20;
                                    fill-rule: evenodd;
                                }
                            </style></defs
                        ><g class="Cheps_Color" id="Layer_2" data-name="Layer 2"><g id="inner"><g id="silhouette"><g id="brim"><path id="under" d="M55.7038,786.7194c-23.2528,1.2868-51.0839-15-55.1608-35.316-8.3864-41.7912,81.7679-110.4816,173.2658-118.0576a217.9743,217.9743,0,0,1,80.4446,8.7426c-53.5856,18.0542-87.9687,43.4317-109.93,64.2053C101.8605,746.46,89.5208,784.848,55.7038,786.7194Z"/><path id="top" d="M22.21,654.9829c-3.4034,5.0733,3.0948,10.9083,7.764,6.9686a248.7022,248.7022,0,0,1,79.5213-45.3374,257.82,257.82,0,0,1,81.2711-13.3407c44.354-.226,99.6682,12.4378,173.5248,57.0312C591.9434,797.758,641.6241,843.7008,889.2993,599.6952a10.78,10.78,0,0,0-3.7981-17.7768c-73.0833-27.2966-382.4656-138.4421-534.9564-122.9837-4.781.4848-14.4487,1.4732-27.0192,3.82C280.9582,470.7,248.1542,486.13,176.81,530.3522,67.69,597.99,46.5855,622.5442,36.035,635.962,30.8947,642.4994,26.3013,648.8835,22.21,654.9829Z"/></g><path id="cap" d="M1132.3525,622.427a18.4469,18.4469,0,0,1-25.62,15.6187c-75.81-32.2188-139.0548-57.1511-183.9679-74.3809l-.0031-.0013S648.7723,453.135,421.4571,429.858c-25.8325-2.6452-71.8146-6.1777-126.2718,9.3439a304.9666,304.9666,0,0,0-30.0561,10.2942,9.3736,9.3736,0,0,1-12.6464-10.7345c13.9663-62.8265,70.4084-257.6986,248.38-339.2313,102.1887-46.8147,207.0631-38.1757,240.165-35.6879,84.6775,6.364,143.6679,33.8041,179.7415,55.8124C1070.4913,211,1148.0149,405.5746,1132.3525,622.427Z"/><path id="button" d="M785.8951,41.2948a451.5875,451.5875,0,0,0-108.4-6.3987c-1.9116-3.8345-3.1894-8.3269-2.1293-12.8731C679.219,5.4954,711.4987-1.13,733.9347.1556c22.5635,1.2924,53.945,11.6569,55.7689,28.4469C790.25,33.6284,788.0423,38.1149,785.8951,41.2948Z"/></g></g></g></svg
                    >
                {/if}
            {/each}
        </div>
    {/each}
</div>

{#if minutes.at(-1) === "1"}
    <p class="footer" transition:fade>
        Made with <svg
            viewBox="0 0 1792 1792"
            preserveAspectRatio="xMidYMid meet"
            xmlns="http://www.w3.org/2000/svg"
            ><path
                class="heart"
                class:odd_heart
                d="M896 1664q-26 0-44-18l-624-602q-10-8-27.5-26T145 952.5 77 855 23.5 734 0 596q0-220 127-344t351-124q62 0 126.5 21.5t120 58T820 276t76 68q36-36 76-68t95.5-68.5 120-58T1314 128q224 0 351 124t127 344q0 221-229 450l-623 600q-18 18-44 18z"
            ></path></svg
        > by RootM
    </p>
{:else}
    <p class="footer" transition:fade>
        Fork me on <span class="url" class:odd_heart
            >github.com/stagrim/basta</span
        >
    </p>
{/if}

<style lang="scss">
    //TODO: break out some styling parts to different files
    @font-face {
        font-family: "Roboto Mono";
        src: url("/RobotoMono-VariableFont_wght.ttf");
    }

    $background: #0e0e0e;
    $inactive: #2e2e2e;
    $main: #f280a1;
    $secondary: #9966cc;
    $pinkCheps: "";

    $diameter: 16vw;
    $diameter-height-breakpoint: 26vh;

    $diameter-vertical: 15vh;
    $diameter-height-breakpoint-vertical: 26vw;

    :global(body) {
        background-color: $background;
        font-size: 16px;
    }

    * {
        font-family: "Roboto", "Helvetica", "Arial", sans-serif;
    }

    .container {
        display: flex;
        flex-direction: column;
        align-items: center;
        margin-top: 2%;
    }

    .row {
        display: flex;
    }

    .circle {
        width: min($diameter, $diameter-height-breakpoint);
        height: $diameter;
        max-height: $diameter-height-breakpoint;
        margin: 5px;
        //background-color: $inactive;
        fill: $inactive;
        transition: 0.1s;

        display: flex;
        justify-content: center;
        vertical-align: middle;
        align-items: center;
        color: $inactive;
    }

    .circle.secondary.show_inactive_numbers {
        color: $secondary;
    }

    .Cheps_Color {
        fill: $inactive;
    }

    .circle.active .Cheps_Color {
        //background-color: $main;
        //color: $inactive;
        fill: $main;
    }

    .circle.secondary.active .Cheps_Color {
        //background-color: $secondary;
        fill: $secondary;
    }

    .circle .legend {
        font-family: Roboto Mono;
        font-weight: 700;
        font-size: 3.5rem;
    }

    .footer {
        bottom: 0;
        width: 100%;
        position: fixed;
        text-align: center;
        color: #787878;
        font-size: 1.66rem;
    }

    .footer .url {
        color: $main;
        text-decoration: underline;
    }

    .footer .url.odd_heart {
        color: $secondary;
    }

    .heart {
        fill: $main;
        transition: 1s;
    }

    .footer svg {
        height: 1.8rem;
    }

    .heart.odd_heart {
        fill: $secondary;
    }

    @media (orientation: portrait) {
        .clock {
            box-shadow: 0 0 0 0.5vh $main inset;
        }

        .container {
            justify-content: center;
            flex-direction: row;
        }

        .row {
            display: flex;
            flex-direction: column-reverse;
        }

        .circle {
            height: min(
                $diameter-vertical,
                $diameter-height-breakpoint-vertical
            );
            width: $diameter-vertical;
            max-width: $diameter-height-breakpoint-vertical;
        }

        .tomte {
            height: min($diameter, $diameter-height-breakpoint);
        }
    }

    .tomte {
        width: min($diameter, $diameter-height-breakpoint + 0.3vw);
        max-height: $diameter-height-breakpoint;
        height: $diameter;
        left: calc(($diameter / 2) - 3.2vw);
        top: 46vh;
        position: absolute;
    }

    // Snow effects

    :global(body) {
        /* height: 100vh; */
        /* background: radial-gradient(ellipse at bottom, #1b2735 0%, #090a0f 100%); */
        overflow: hidden;
    }

    @function random_range($min, $max) {
        $rand: random();
        $random_range: $min + floor($rand * (($max - $min) + 1));
        @return $random_range;
    }

    .snow {
        // filter: drop-shadow(0 0 10px white);
        // $total: 200;
        position: absolute;
        z-index: 10;
        width: 10px;
        height: 10px;
        // background: white;
        // border-radius: 50%;
        color: white;

        @for $i from 1 through 1000 {
            $random-x: random(1000000) * 0.0001vw;
            $random-offset: random_range(-100000, 100000) * 0.0001vw;
            $random-x-end: $random-x + $random-offset;
            $random-x-end-yoyo: calc($random-x + ($random-offset / 2));
            $random-yoyo-time: calc(random_range(30000, 80000) / 100000);
            $random-yoyo-y: $random-yoyo-time * 100vh;
            $random-scale: random(3);
            $fall-duration: random_range(10, 30) * 1s;
            $fall-delay: random(30) * -1s;
            $rotate: random(720) * 1deg - 360deg;

            &:nth-child(#{$i}) {
                opacity: random(7000) * 0.0001 + 0.3;
                transform: translate($random-x, -60px)
                    scale($random-scale)
                    rotate($rotate);
                animation: fall-#{$i}
                    $fall-duration
                    $fall-delay
                    linear
                    infinite;
            }

            @keyframes fall-#{$i} {
                #{percentage($random-yoyo-time)} {
                    transform: translate($random-x-end, $random-yoyo-y)
                        scale($random-scale);
                }

                to {
                    transform: translate($random-x-end-yoyo, 100vh)
                        scale($random-scale) rotate($rotate);
                }
            }
        }
    }

    // Analog clock

    .clock {
        box-shadow: 0 0 0 0.5vw $main inset;
        display: flex;
        justify-content: center;
        position: relative;
    }

    $blob-diameter: 10%;
    #blob {
        position: absolute;
        background: $main;
        width: $blob-diameter;
        height: $blob-diameter;
        border-radius: 50%;
    }

    #hour,
    #minute,
    #second {
        position: absolute;
        background-color: $main;
        transform-origin: bottom center;
        border-radius: 1px;
        transition: 0.99s linear;
    }

    .active {
        #hour,
        #minute,
        #second,
        #blob {
            background: $inactive;
        }
    }

    $hour-length: 20%;
    #hour {
        width: 5%;
        height: $hour-length;
        margin-top: -$hour-length;
    }

    $minute-length: 35%;
    #minute {
        width: 5%;
        height: $minute-length;
        margin-top: -$minute-length;
    }

    $second-length: 40%;
    #second {
        width: 2%;
        height: $second-length;
        margin-top: -$second-length;
    }
</style>
