<template>
  <div>
  <div class="text-white text-center">
  <h1>
    THE GRUMPY CAT GAME
  </h1>
</div>
<div>
  <img class="😸" src="../assets/grumpycatno.png" alt="grumpy cat">
</div>
<div>
  <img class="🥣" src="../assets/bowl.png" alt="cat bowl">
</div>
<div>
  <img class="🍩 🍽" src="../assets/donut.png" alt="donut">
  <img class="🍟 🍽" src="../assets/fries.png" alt="fries">
  <img class="🐟 🍽" src="../assets/goldfish.png" alt="goldfish">
  <img class="🍔 🍽" src="../assets/hamburger.png" alt="hamburger">
  <img class="🍕 🍽" src="../assets/pizza.png" alt="pizza">
  <img class="🌮 🍽" src="../assets/taco.png" alt="taco">
  <img class="🌭 🍽" src="../assets/hotdog.png" alt="hotdog">
</div>
<div class="scoreboard-styling">
  <img src="../assets/scoreboard.png" alt="score board">
</div>
<div class="counter-cat">{{counterCat}}</div>
<div class="counter-you">{{counterYou}}</div>
</div>
</template>

<script>
import { fromEvent } from "rxjs/observable/fromEvent";
import { Subscription } from "rxjs/Subscription";
import { Subject } from "rxjs/Subject";
import { filter, mergeMap, tap, takeUntil, map, scan } from "rxjs/operators";

export default {
  name: "GrumpyCat",
  data: () => ({
    counterCat: 0,
    counterYou: 0
  }),
  methods: {
    hasBeenFedToGrumpyCat: (x, y) => {
      return x > 0 && x < 700 && y > 400 && y < 700;
    }
  },
  mounted: function() {
    const mouseDown$ = fromEvent(document, "mousedown");
    const mouseMove$ = fromEvent(document, "mousemove");
    const mouseUp$ = fromEvent(document, "mouseup");
    const increment$ = new Subject();

    const counterCat$ = increment$.pipe(
      filter(({ clientX, clientY }) => this.hasBeenFedToGrumpyCat(clientX, clientY)),
      filter(e => e.target.matches(".🐟, .🌭, .🍔")),
      scan(count => count + 1, 0)
    );

    const counterYou$ = increment$.pipe(
      filter(({ clientX, clientY }) => this.hasBeenFedToGrumpyCat(clientX, clientY)),
      filter(e => e.target.matches(".🍟, .🍕, .🍩, .🌮")),
      scan(count => count + 1, 0)
    );

    const targetMouseDown$ = mouseDown$.pipe(
      filter(e => e.target.matches(".🍽"))
    );

    const mouseDrag$ = targetMouseDown$.pipe(
      mergeMap(({ target: draggable, offsetX: startX, offsetY: startY }) =>
        mouseMove$.pipe(
          tap(mouseMoveEvent => {
            mouseMoveEvent.preventDefault();
          }),
          map(mouseMoveEvent => ({
            left: mouseMoveEvent.clientX - startX,
            top: mouseMoveEvent.clientY - startY,
            draggable
          })),
          takeUntil(mouseUp$.pipe(tap(increment$)))
        )
      )
    );
    this.subscriptionA = counterCat$.subscribe(
      count => (this.counterCat = count)
    );
    this.subscriptionB = counterYou$.subscribe(
      count => (this.counterYou = count)
    );
    this.subscriptionC = mouseDrag$.subscribe(({ top, left, draggable }) => {
      draggable.style.top = top + "px";
      draggable.style.left = left + "px";
    });
  },
  beforeDestroy: () => {
    this.subscriptionA.unsubscribe();
    this.subscriptionB.unsubscribe();
    this.subscriptionC.unsubscribe();
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.text-center {
  text-align: center;
}

.text-white {
  color: white;
}

.scoreboard-styling {
  position: absolute;
  top: 150px;
  left: 600px;
}

.😸 {
  position: relative;
  top: 80px;
}

.🥣 {
  position: absolute;
}

.🍩 {
  position: absolute;
  left: 40px;
  top: 60px;
}

.🍟 {
  position: absolute;
  left: 250px;
  top: 90px;
}

.🐟 {
  position: absolute;
  left: 820px;
  top: 580px;
}

.🍔 {
  position: absolute;
  left: 1000px;
  top: 680px;
}

.🌭 {
  position: absolute;
  left: 430px;
  top: 160px;
}

.🍕 {
  position: absolute;
  left: 780px;
  top: 740px;
}

.🌮 {
  position: absolute;
  left: 1200px;
  top: 600px;
}

.🍽 {
  cursor: -webkit-grab;
  cursor: grab;
}

.🍽:active {
  cursor: -webkit-grabbing;
  cursor: grabbing;
}

.counter-cat {
  position: absolute;
  left: 1140px;
  top: 393px;
  font-family: Impact;
  font-size: 80px;
  color: red;
}

.counter-you {
  position: absolute;
  left: 705px;
  top: 393px;
  font-family: Impact;
  font-size: 80px;
  color: red;
}
</style>
