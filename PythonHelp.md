# Python commands and workflows for Houdini
[Houdini python API](https://www.sidefx.com/docs/houdini/hom/hou/index.html)

## Basic Stuff
### Structure of python code in a python node
First define the required functions and call them under main
```
def main():
    print "Hello"
    

main()
``` 


### Access node properties
To read a node property in a python node use:
```
param = node.parm('parameter_name').eval()
```

### Read file line by line
To read a file line by line until the end of file use the following loop:
```
f = open(filename,'r')
for line in f:
    print line
    # or if you want to split the line in spaces
    temp = line.split(' ')

# Never forget to close the file
f.close()
```
or using while and read lines inside the loop
```
while True:
    line = f.readline()
    if ("" == line):
        break

```

### Splitting text
- split with one delimiter
```
temp = line.split(' ')
temp = line.split(',')
```
- split with multiple delimiters </br>
for example to check for tab, space and endline use

```
import re
temp = re.split('\t | |\n',line)
```
### Convert splitted string to numbers
The splitted string is an array
```
i = int(temp[0])
f = float(temp[1])
```
### Convert number to string
- Simple
```
txt = str(num)
```
- With padding zeros
```
txt = str(numstr).zfill(3)
```

### Remove empty elements of a list
If `temp` is a list that contains empty elements these can be removed using
```
temp = list(filter(None, temp))
```

### Write files
Open file for reading. Don't forget to close it
```
f = open(filename,'w')
f.write(str(x) + " " + str(y) + " " + str(z) + "\n")
f.close()
```
### Loop through geometry
* Points : See [iterPoints](https://www.sidefx.com/docs/houdini/hom/hou/Geometry.html) function
```
for point in geo.points():
    x = point.position()[0]
    y = point.position()[2]
    z = point.position()[1]
```




## Create Geometry
### Points
First specify the attribut names
```
geo.addAttrib(hou.attribType.Point, "ID", 0) # This is integer
geo.addAttrib(hou.attribType.Point, "Vel", 0.0) # This is float
```
Create the point:
```
point = geo.createPoint() # create point on origin
point.setPosition(hou.Vector3(x,z,y)) # set position
point.setAttribValue("ID", 1) # set attributes
point.setAttribValue("Vel", 5.78) # set attributes
```
### Polygons
Define the polygon attributes
```
geo.addAttrib(hou.attribType.Prim, "ID", 0) # Integer
geo.addAttrib(hou.attribType.Prim, "Rch", 0.0) # float
```
Create polygon
```
poly = geo.createPolygon()
poly.addVertex(point1)
poly.addVertex(point2)
poly.addVertex(point3)
etc...

poly.setAttribValue("ID", 123)
poly.setAttribValue("Rch", 0.004)
```
The `point` input argument of `addVertex` is the output of `geo.createPoint()`. Most of the times first you make the points and then append to the primitive

### Create Bazier Curve
This is a bit more difficult compared to points and polygons.
The command `createBezierCurve` requires the number of points included in the curve. Therefore I typically make an array of hou vectors with the positions, then initialize the curve with points on the origin and then loop through the points of the curve and update the point attibutes. For example
```
Plist = [] # This is an empty array
# populate the array with points
... loop:
    Plist.append(hou.Vector3(x, y, z))

# Create the curve
curve = geo.createBezierCurve(len(Plist),False,2)
# set the curve point position and any other attibute
i = 0
for p in curve.points():
    p.setPosition(Plist[i])
    i = i + 1
```

### Add global attributes
```
geo.addAttrib(hou.attribType.Global, "NL", 0); # define attribute
geo.setGlobalAttribValue("NL", NL) # Set value
```
