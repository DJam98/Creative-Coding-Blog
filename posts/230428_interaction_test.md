---
title: Testing Interaction in Deno Blog
publish_date: 2023-04-28
disable_html_sanitization: true
---

# I am testig this - woo!



<canvas id=onclick_example></canvas>

<script type=module>

    // getting and formatting the canvas element
    const cnv = document.getElementById (`onclick_example`)
    cnv.width = cnv.parentNode.scrollWidth
    cnv.height = cnv.width * 9 / 16

    // this array will store the coordinates
    // of the click events
    const coordinates = []

    // this function will take the
    // pointerEvent as an argument
    // and assign it to parameter 'e'
    function add_coordinate (e) {

        // adding to the coordinates array 
        // an object with x & y properties
        // storing the values associated 
        // with the .offsetX and .offsetY
        // properties of the pointerEvent
        // object assigned to parameter 'e' 
        coordinates.push ({
            x : e.offsetX,
            y : e.offsetY
        })
    }

    // adding the function to the 
    // .onclick property of the canvas
    // add_coordinate
    cnv.onclick = add_coordinate

    // getting a 2d context
    const ctx = cnv.getContext ('2d')    

    // function to draw animation frames
    function draw_frame () {

        // turquoise background
        ctx.fillStyle = `turquoise`
        ctx.fillRect (0, 0, cnv.width, cnv.height)

        // hotpink squares
        ctx.fillStyle = `hotpink`

        // go through the coordinates array
        coordinates.forEach (p => {

            // use the values on the x & y properties
            // of each object to draw a square
            ctx.fillRect (p.x - 10, p.y - 10, 20, 20)
        })

        // call itself recursively
        requestAnimationFrame (draw_frame)
    }

    // call the first frame
    requestAnimationFrame (draw_frame)
</script>



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