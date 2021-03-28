<script>
    import { onMount } from "svelte";
    import Konva from "konva";

    let isDragging = false;
    let width;
    let height;
    let itemURL = "";
    let stage;
    let layer;
    let container;
    let gameActive = true;

    let answer = 2 + Math.floor(Math.random() * 4);

    const submitAnswer = (e) => {
        const imageCollection = layer.find("Image");
        const num = imageCollection.length;

        if (num === answer) {
            gameActive = false;

            imageCollection.each((shape, n) => {
                shape.destroy();
            });
        }
    };

    const resetGame = (e) => {
        gameActive = true;
        layer.draw();
        answer = 2 + Math.floor(Math.random() * 4);
    };

    const handleDragStart = (e) => {
        isDragging = true;
        itemURL = e.target.src;
    };

    const handleDragEnd = (e) => {
        isDragging = false;
        itemURL = "";
    };

    const handleCanvasDrop = (e) => {
        e.preventDefault();
        // now we need to find pointer position
        // we can't use stage.getPointerPosition() here, because that event
        // is not registered by Konva.Stage
        // we can register it manually:
        stage.setPointersPositions(e);

        Konva.Image.fromURL(itemURL, function (image) {
            layer.add(image);

            image.position(stage.getPointerPosition());
            image.draggable(true);

            image.on("mouseover", (e) => {
                document.body.style.cursor = "grab";
            });

            image.on("mousedown", (e) => {
                document.body.style.cursor = "grabbing";
            });

            // not working
            image.on("mouseup", (e) => {
                document.body.style.cursor = "grab";
            });

            image.on("mouseout", (e) => {
                document.body.style.cursor = "default";
            });

            layer.draw();
        });
    };

    onMount(() => {
        container = document.getElementById("container");

        width = container.clientWidth;
        height = container.clientHeight;

        stage = new Konva.Stage({
            container: "container",
            width: width,
            height: height,
        });

        layer = new Konva.Layer();

        const rect = new Konva.Rect({
            x: 1,
            y: 1,
            width: 240,
            height: 100,
            fill: "red",
            stroke: "black",
            opacity: 0,
            strokeWidth: 1,
        });
        layer.add(rect);

        stage.add(layer);

        layer.on("dragstart", (e) => {
            rect.opacity(0.5);
            layer.draw();
        });

        layer.on("dragend", (e) => {
            const target = e.target;
            const clientRect = target.getClientRect();

            // delete zone bounds
            if (clientRect.x < 240 && clientRect.y < 100) {
                target.destroy();
                layer.draw();
                document.body.style.cursor = "default";
            }
            rect.opacity(0);
            layer.draw();
        });
    });
</script>

<div class="game-wrapper">
    <section>
        <p>
            {#if gameActive}
                Can you give me {answer} coffees?
            {:else}
                Correct!
            {/if}
        </p>
        <div
            id="container"
            on:dragover|preventDefault
            on:drop={handleCanvasDrop}
        />
    </section>
    <aside>
        <div
            draggable="true"
            on:dragstart={handleDragStart}
            on:dragend={handleDragEnd}
            class="drag-item"
            class:is-dragging={isDragging}
        >
            <img src="images/coffee.svg" alt="coffee" />
        </div>
        {#if gameActive}
            <button on:click|preventDefault={submitAnswer}>SUBMIT ANSWER</button
            >
        {:else}
            <button on:click|preventDefault={resetGame}>NEW GAME</button>
        {/if}
    </aside>
</div>

<style>
    .game-wrapper {
        display: flex;
        flex-direction: row;
        height: 100%;
    }

    #container {
        background-color: lightgray;
        width: 100%;
        height: 100%;
    }

    section {
        overflow: clip;
        width: 80%;
        height: inherit;
        border: solid black 2px;
    }

    aside {
        /* flex-grow: 1; */
        width: 20%;
        border: solid black 2px;
    }

    .drag-item {
        cursor: move;
    }

    .is-dragging {
        cursor: copy;
    }
</style>
