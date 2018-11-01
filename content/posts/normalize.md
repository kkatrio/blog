---
title: "Normalize"
date: 2018-11-01T22:05:06+02:00
draft: false
---

Some results on a project I am working on were slightly skewed and this is why:

```
template <typename Point>
struct Vector
{
  double x, y;
  Vector() = default; // doubles initialize to 0
  Vector(Point& A, Point& B) : x(B.x - A.x), y(B.y - A.y) {}

  void normalize()
  {
    x /= norm();
    y /= norm();
  }

  double norm()
  {
    return std::sqrt(x * x + y * y);
  }

};
```

An hour of my life to debug it that I'm never getting back.
