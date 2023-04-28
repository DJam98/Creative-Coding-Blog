---
title: Testing Interaction in Deno Blog
publish_date: 2023-04-28
disable_html_sanitization: true
---

# I am testig this - woo!


<script>
    class Shrinker {

    // position specifies the middle of the object
    // object also needs a size
    // and a canvas context to draw to
    constructor (position, size, context) {
        this.pos = position
        this.siz = size
        this.ctx = context

        // we will use these properties to control
        // the shrinking and growing animation
        this.active = false
        this.phase  = 0
    }

    draw () {

        // if active, increment phase
        if (this.active) {
            this.phase += 0.01
        }

        // if phase is complete
        // disable object and reset phase
        if (this.phase > 1) {
            this.active = false
            this.phase  = 0
        }

        // this mathematics creates the envelope
        // that will shrink / grow the square
        // double goes from 0 - 2
        const double = this.phase * 2

        // rev goes from 2 - 0
        const rev = 2 - double

        // env = whichever one is less
        // env goes from 0 -> 1 -> 0
        const env = Math.min (double, rev)

        // mult goes from 1 -> 0 -> 1
        const mult = 1 - env

        // calculate the size under the envelope
        const len = this.siz * mult

        // calculate the position under the envelope
        const x = this.pos.x - (len / 2)
        const y = this.pos.y - (len / 2)

        // draw the pink square
        // using the values calculated
        this.ctx.fillStyle = `hotpink`
        this.ctx.fillRect (x, y, len, len)
    }
}

</script>

<canvas id=onpointermove_example></canvas>

<script type=module>
    const cnv = document.getElementById (`onpointermove_example`)
    cnv.width = cnv.parentNode.scrollWidth
    cnv.height = cnv.width * 9 / 16

    // assigning to the onpointermove property
    // a handler defined below
    cnv.onpointermove = pointer_move_handler

    const ctx = cnv.getContext ('2d')

    // calculating the dimension
    // for the Shrinker objects
    // the aspect ratio is 16:9
    // so this will act as both
    // the width and height
    const w = cnv.width / 16

    // array for the Shrinker objects
    const shrinkers = []

    // iterate down the canvas using the width value
    for (let y = w / 2; y < cnv.height; y += w) {

        // iterate across the canvas using the same value
        for (let x = w / 2; x < cnv.width; x += w) {

            // make an object with x & y properties
            // assign to those properties the x & y 
            // values as per the for loops, using
            // object literal syntax
            const vec = {
                // property name on the left of the :
                // value (ie. variable) on the right
                x: x, 

                // property name on the left of the :
                // value (ie. variable) on the right
                y: y, 
            }

            // add to the array, a Shrinker object
            // with those coordinates,
            // and with size w, and also passing in
            // the canvas context
            shrinkers.push (new Shrinker (vec, w, ctx))
        }
    }

    // function assigns the pointerEvent
    // to parameter 'e'
    function pointer_move_handler (e) {

        // expresses the coordinates as a
        // phase between 0-1
        const x_phase = e.offsetX / cnv.width
        const y_phase = e.offsetY / cnv.height

        // find the column and row numbers
        const col = Math.floor (x_phase * 16)
        const row = Math.floor (y_phase * 9)

        // the index of the Shrinker object
        // because they were added to the array
        // row by row
        const i = row * 16 + col
        
        // if the shrinker at that index is not active
        if (!shrinkers[i].active) {

            // activate it
            shrinkers[i].active = true
        }
    }

    function draw_frame () {

        // turquoise background
        ctx.fillStyle = `turquoise`
        ctx.fillRect (0, 0, cnv.width, cnv.height)

        // go through the shrinkers array
        // call .draw () on each Shrinker object
        shrinkers.forEach (s => s.draw ())

        // call the next animation frame
        requestAnimationFrame (draw_frame)
    }

    // call the first animation frame
    requestAnimationFrame (draw_frame)
</script>