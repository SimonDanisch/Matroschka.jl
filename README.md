# Matroschka

[![Build Status](https://travis-ci.com/SimonDanisch/Matroschka.jl.svg?branch=master)](https://travis-ci.com/SimonDanisch/Matroschka.jl)
[![Codecov](https://codecov.io/gh/SimonDanisch/Matroschka.jl/branch/master/graph/badge.svg)](https://codecov.io/gh/SimonDanisch/Matroschka.jl)

![](https://erlangenwladimir.files.wordpress.com/2015/05/matrjoschka-3.jpg)

Basic package to define a recursive rendering API.
The general idea is, you have a set of objects, you have a package that can draw objects.
Now, you'll call render until you find objects that the drawing package can draw.

E.g:

```julia

struct MyObject
end

function Matroschka.render(context, object::MyObject)
    return "this is my object"
end

"""
I can draw strings
"""
struct StringRenderer
end

function Matroschka.render(context::StringRenderer, object::String)
    println(object)
end
context = StringRenderer()
render(context, MyObject())
```

This is of course the simplest example in existence.
The real magic happens, once you define a 3D renderer, a few objects that the renderer can render...
And then do stuff like this:

```julia
import Matroschka: render

struct Particles{N}
    positions::Vector{Point{N}}
    colors::Vector{RGB}
    markers::Vector{Marker}
end

struct OpenGLRenderer
    # magic opengl stuff
end

function render(context::OpenGLRenderer, particles::Particles)
    # more magic opengl stuff to render particles
end

gl = OpenGLRenderer()

render(gl, particles(rand(10)))

```

Now the real fun begins, when we insert extra context/layers to transform our objects into something renderable.
E.g. we could define a Plotting context, that inserts extra objects like an axes, conversions, and styles into it!
