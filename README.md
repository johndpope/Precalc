# Precalc
This repo contains some code which can graph equations in a UIView.

[![A quick demo](./demo.png)](./demo.png)

Summary:
---

Basically, you define an equation, tell the graph what color to draw in, and CoreGraphics does the rest.

I've been meaning to make something like this during precalculus and then during calculus 1, so before I take calc 2, I'm finally making this thing.

The playground is documented fairly well, so have a look.

Technical:
---

Written during bus and subway commutes using Xcode 7.3.1 and Swift 2.2.

Using It:
---

You can draw one of the predefined graphs by instantiating a `GraphView`, an instance of a `GraphableEquation`, and then adding it to the `GraphView`:

    let graph = GraphView(withSmallerXBound: -15.0, largerXBound: 15.0, andInterval: 0.5)
    let sine = Sine()
    graph.addEquation(sine)
    
Here's the output:

![A graph of a sine wave](./demosin.png)

Adding Your Own Equations:
---

To add your own equation, conform to the `Equation` protocol:

    protocol Equation {
        var coordinates : [Coordinate] { get }
        func compute(withInterval interval: CGFloat, between x1: CGFloat, and x2: CGFloat)
    }
    
You must assign coordinates before exiting the `compute` method, or nothing will draw. Perhaps this should be handled internally to the graph view - I'm not sure yet. 

(An alternate implementation would simplify this protocol and eliminate the coordinates property. In that case, GraphView would handle the caching internally.)
    
The graph view can draw your equation if you implement the compute function and also adopt `GraphableEquation`, which defines the color of your drawing on the graph.

     protocol GraphableEquation : Equation
     {
         var drawingColor : UIColor { get set }
     }
     
Here's an example `GraphableEquation` implementation for the sine formula we used earlier:

````
//: Sine

class Sine : GraphableEquation
{
    var period: CGFloat
    var amplitude: CGFloat
    var phaseShift: CGFloat
    var verticalShift: CGFloat
    
    // MARK: - Initializer
    
    init(period: CGFloat, amplitude: CGFloat, phaseShift: CGFloat, verticalShift: CGFloat)
    {
        self.period = period
        self.amplitude = amplitude
        self.phaseShift = phaseShift
        self.verticalShift = verticalShift
    }
    
    convenience init()
    {
        self.init(period: 1.0, amplitude: 1.0, phaseShift: 0.0, verticalShift: 0.0)
    }
    
    // MARK: - GraphableEquation
    
    var coordinates: [Coordinate] = []
    var drawingColor: UIColor = UIColor.blackColor()
    
    func compute(withInterval interval: CGFloat, between x1: CGFloat, and x2: CGFloat) {
        
        var coordinates : [Coordinate] = []
        
        var x = x1
        
        while x <= x2
        {
            let y : CGFloat
            
            y = amplitude * sin((self.period * x) - (self.phaseShift/self.period)) + self.verticalShift
            
            coordinates.append(Coordinate(x: x, y: y))
            
            x = x + interval
        }
        
        self.coordinates = coordinates
    }
}

````

We just implement the formula for a sine wave, taking into account the possible transformations built into the equation.

The cool thing about this protocol based system is that we can implement convenience initializers specific to our function, and as long as we can supply the graph with coordinates, it will do the right thing. 

For example, our sine equation has an amplitude parameter. A line equation might have a slope and an offset instead. For example:

`let line = Line(slope: 1.0, offset: 3.0)`

There's more information in the playground, so take a look! (If you're feeling ambitious, maybe take a stab at one of these TODO items.)

TODO:
---

- [ ] Research how to implement `for ... in` with `CGFloat`
- [ ] Implement more trigenometric functions
- [ ] Implement parabolas
- [ ] Implement transformation protocol in graph view
- [ ] Investigate and implement calculus stuff, such as shading and representations of estimation methods. 
- [ ] Consider breaking out into a `.framework`
- [ ] GraphView's scaling is pretty cool, but the view size needs some love.


License:
---
MIT
