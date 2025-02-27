---
title: "CurvePolygon"
description: "CurvePolygon is a topologically closed surface defined by an exterior bounding ring and zero or more interior rings in SQL Database Engine spatial data."
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: mlandzic, jovanpop
ms.date: 11/04/2024
ms.service: sql
ms.topic: conceptual
ms.custom:
  - ignite-2024
monikerRange: "=azuresqldb-current || >=sql-server-2016 || >=sql-server-linux-2017 || =azuresqldb-mi-current || =fabric"
---
# CurvePolygon

[!INCLUDE [SQL Server Azure SQL Database Azure SQL Managed Instance Fabric SQL endpoint Fabric DW FabricSQLDB](../../includes/applies-to-version/sql-asdb-asdbmi-fabricse-fabricdw-fabricsqldb.md)]  

  A **CurvePolygon** is a topologically closed surface defined by an exterior bounding ring and zero or more interior rings in SQL Database Engine spatial data.
  
> [!IMPORTANT]  
> For a detailed description and examples of spatial features introduced in [!INCLUDE [ssSQL11](../../includes/sssql11-md.md)], including the **CurvePolygon** subtype, download the white paper, [New Spatial Features in SQL Server 2012](https://go.microsoft.com/fwlink/?LinkId=226407).  
  
 The following criteria define attributes of a **CurvePolygon** instance:  
  
-   The boundary of the **CurvePolygon** instance is defined by the exterior ring and all interior rings.  
  
-   The interior of the **CurvePolygon** instance is the space between the exterior ring and all of the interior rings.  
  
 A **CurvePolygon** instance differs from a **Polygon** instance in that a **CurvePolygon** instance can contain the following circular arc segments: **CircularString** and **CompoundCurve**.  
  
## CompoundCurve instances
 Illustration below shows valid **CurvePolygon** figures:  
  
### Accepted instances
 For a **CurvePolygon** instance to be accepted, it needs to be either empty or contain only circular arc rings that are accepted. An accepted circular arc ring meets the following requirements.  
  
1. Is an accepted **LineString**, **CircularString**, or **CompoundCurve** instance. For more information on accepted instances, see [LineString](linestring.md), [CircularString](circularstring.md), and [CompoundCurve](compoundcurve.md).  
  
1. Has at least four points.  
  
1. The start and endpoint have the same X and Y coordinates.  

    > [!NOTE]  
    > Z and M values are ignored.  
  
The following example shows accepted **CurvePolygon** instances.  
  
```sql  
DECLARE @g1 geometry = 'CURVEPOLYGON EMPTY';  
DECLARE @g2 geometry = 'CURVEPOLYGON((0 0, 0 0, 0 0, 0 0))';  
DECLARE @g3 geometry = 'CURVEPOLYGON((0 0 1, 0 0 2, 0 0 3, 0 0 3))'  
DECLARE @g4 geometry = 'CURVEPOLYGON(CIRCULARSTRING(1 3, 3 5, 4 7, 7 3, 1 3))';  
DECLARE @g5 geography = 'CURVEPOLYGON((-122.3 47, 122.3 -47, 125.7 -49, 121 -38, -122.3 47))';  
```  
  
`@g3` is accepted even though the start and end points have different Z values because Z values are ignored. `@g5` is accepted even though the **geography** type instance is not valid.  
  
The following examples throw `System.FormatException`.  
  
```sql  
DECLARE @g1 geometry = 'CURVEPOLYGON((0 5, 0 0, 0 0, 0 0))';  
DECLARE @g2 geometry = 'CURVEPOLYGON((0 0, 0 0, 0 0))';  
```  
  
`@g1` is not accepted because the start and end points do not have the same Y value. `@g2` is not accepted because the ring does not have enough points.  
  
### Valid instances
For a **CurvePolygon** instance to be valid both exterior and interior rings must meet the following criteria:  
  
1. They can only touch at single tangent points.  
1. They cannot cross each other.  
1. Each ring must contain at least four points.  
1. Each ring must be an acceptable curve type.  
  
**CurvePolygon** instances also need to meet specific criteria depending on whether they are **geometry** or **geography** data types.  
  
#### Geometry data type
A valid **geometryCurvePolygon** instance must have the following attributes:  
  
1. All the interior rings must be contained within the exterior ring.  
1. May have multiple interior rings, but an interior ring cannot contain another interior ring.  
1. No ring can cross itself or another ring.  
1. Rings can only touch at single tangent points (number of points where rings touch must be finite).  
1. The interior of the polygon must be connected.  
  
The following example shows valid **geometryCurvePolygon** instances.  
  
```sql  
DECLARE @g1 geometry = 'CURVEPOLYGON EMPTY';  
DECLARE @g2 geometry = 'CURVEPOLYGON(CIRCULARSTRING(1 3, 3 5, 4 7, 7 3, 1 3))';  
SELECT @g1.STIsValid(), @g2.STIsValid();  
```  
  
CurvePolygon instances have the same validity rules as Polygon instances with the exception that CurvePolygon instances can accept the new circular arc segment types. For more examples of instances that are valid or not valid, see [Polygon](polygon.md).  
  
#### Geography data type
A valid **geographyCurvePolygon** instance must have the following attributes:  
  
1. The interior of the polygon is connected using the left-hand rule.  
1. No ring can cross itself or another ring.  
1. Rings can only touch at single tangent points (number of points where rings touch must be finite).  
1. The interior of the polygon must be connected.  
  
The following example shows a valid geography CurvePolygon instance.  
  
```sql  
DECLARE @g geography = 'CURVEPOLYGON((-122.3 47, 122.3 47, 125.7 49, 121 38, -122.3 47))';  
SELECT @g.STIsValid();  
```  
  
## Examples
  
<a id="a-instantiating-a-geometry-instance-with-an-empty-curvepolygon"></a>

### A. Instantiate a Geometry Instance with an Empty CurvePolygon
 This example shows how to create an empty **CurvePolygon** instance:  
  
```sql  
DECLARE @g geometry;  
SET @g = geometry::Parse('CURVEPOLYGON EMPTY');  
```  
  
<a id="b-declaring-and-instantiating-a-geometry-instance-with-a-curvepolygon-in-the-same-statement"></a>

### B. Declare and Instantiating a Geometry Instance with a CurvePolygon in the Same Statement
 This code snippet shows how to declare and initialize a geometry instance with a **CurvePolygon** in the same statement:  
  
```sql  
DECLARE @g geometry = 'CURVEPOLYGON(CIRCULARSTRING(2 4, 4 2, 6 4, 4 6, 2 4))'  
```  
  
<a id="c-instantiating-a-geography-instance-with-a-curvepolygon"></a>

### C. Instantiate a Geography Instance with a CurvePolygon
 This code snippet shows how to declare and initialize a **geography** instance with a **CurvePolygon**:  
  
```sql  
DECLARE @g geography = 'CURVEPOLYGON(CIRCULARSTRING(-122.358 47.653, -122.348 47.649, -122.348 47.658, -122.358 47.658, -122.358 47.653))';  
```  
  
<a id="d-storing-a-curvepolygon-with-only-an-exterior-bounding-ring"></a>

### D. Store a CurvePolygon with Only an Exterior Bounding Ring
 This example shows how to store a simple circle in a **CurvePolygon** instance (only an exterior bounding ring is used to define the circle):  
  
```sql  
DECLARE @g geometry;  
SET @g = geometry::Parse('CURVEPOLYGON(CIRCULARSTRING(2 4, 4 2, 6 4, 4 6, 2 4))');  
SELECT @g.STArea() AS Area;  
```  
  
<a id="e-storing-a-curvepolygon-containing-interior-rings"></a>

### E. Store a CurvePolygon Containing Interior Rings
 This example creates a donut in a **CurvePolygon** instance (both an exterior bounding ring and an interior ring is used to define the donut):  
  
```sql  
DECLARE @g geometry;  
SET @g = geometry::Parse('CURVEPOLYGON(CIRCULARSTRING(0 4, 4 0, 8 4, 4 8, 0 4), CIRCULARSTRING(2 4, 4 2, 6 4, 4 6, 2 4))');  
SELECT @g.STArea() AS Area;  
```  
  
 This example shows both a valid **CurvePolygon** instance and an invalid instance when using interior rings:  
  
```sql  
DECLARE @g1 geometry, @g2 geometry;  
SET @g1 = geometry::Parse('CURVEPOLYGON(CIRCULARSTRING(0 5, 5 0, 0 -5, -5 0, 0 5), (-2 2, 2 2, 2 -2, -2 -2, -2 2))');  
IF @g1.STIsValid() = 1  
  BEGIN  
     SELECT @g1.STArea();  
  END  
SET @g2 = geometry::Parse('CURVEPOLYGON(CIRCULARSTRING(0 5, 5 0, 0 -5, -5 0, 0 5), (0 5, 5 0, 0 -5, -5 0, 0 5))');  
IF @g2.STIsValid() = 1  
  BEGIN  
     SELECT @g2.STArea();  
  END  
SELECT @g1.STIsValid() AS G1, @g2.STIsValid() AS G2;  
```  
  
 Both `@g1` and `@g2` use the same exterior bounding ring: a circle with a radius of 5 and they both use a square for an interior ring.  However, the instance `@g1` is valid, but the instance `@g2` is invalid. The reason that @g2 is invalid is that the interior ring splits the interior space bounded by the exterior ring into four separate regions. The following drawing shows what occurred:  
  
## Related content

- [Polygon](polygon.md)
- [CircularString](circularstring.md)
- [CompoundCurve](compoundcurve.md)
- [geometry Data Type Method Reference](../../t-sql/spatial-geometry/spatial-types-geometry-transact-sql.md)
- [geography Data Type Method Reference](../../t-sql/spatial-geography/stequals-geography-data-type.md)
- [Spatial Data Types Overview](spatial-data-types-overview.md)
