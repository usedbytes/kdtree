# kdtree

[![GoDoc](https://godoc.org/github.com/Kyroy/kdtree?status.svg)](https://godoc.org/github.com/Kyroy/kdtree)
[![Build Status](https://jenkins.kyroy.com/job/github.com-kyroy/job/kdtree/job/master/badge/icon)](https://jenkins.kyroy.com/job/github.com-kyroy/job/kdtree/job/master/)
[![Jenkins tests](https://img.shields.io/jenkins/t/https/jenkins.kyroy.com/job/github.com-kyroy/job/kdtree/job/master.svg)](https://jenkins.kyroy.com/job/github.com-kyroy/job/kdtree/job/master/)
[![Jenkins coverage](https://img.shields.io/jenkins/c/https/jenkins.kyroy.com/job/github.com-kyroy/job/kdtree/job/master.svg)](https://jenkins.kyroy.com/job/github.com-kyroy/job/kdtree/job/master/)
[![Go Report Card](https://goreportcard.com/badge/github.com/kyroy/kdtree)](https://goreportcard.com/report/github.com/kyroy/kdtree)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://github.com/Kyroy/kdtree/blob/master/LICENSE)

A [k-d tree](https://en.wikipedia.org/wiki/K-d_tree) implementation in Go with:
- n-dimensional points
- k-nearest neighbor search
- remove without rebuilding the whole subtree
- data attached to the points
- using own structs by implementing a simple 2 function interface 

Future features:
- range search


## Usage

```bash
go get github.com/kyroy/kdtree
```

```go
import "github.com/kyroy/kdtree"
````



### Implement the `kdtree.Point` interface

```go
// Point specifies one element of the k-d tree.
type Point interface {
	// Dimensions returns the total number of dimensions
	Dimensions() int
	// Dimension returns the value of the i-th dimension
	Dimension(i int) float64
}
```


### `points.Point2d`

```go
type Data struct {
	value string
}

func main() {
	tree := kdtree.New([]kdtree.Point{
		&points.Point2D{X: 3, Y: 1},
		&points.Point2D{X: 5, Y: 0},
		&points.Point2D{X: 8, Y: 3},
	})

	// Insert
	tree.Insert(&points.Point2D{X: 1, Y: 8})
	tree.Insert(&points.Point2D{X: 7, Y: 5})

	// KNN (k-nearest neighbor)
	fmt.Println(tree.KNN(&points.Point{Coordinates: []float64{1, 1, 1}}, 2))
	// [{3.00 1.00} {5.00 0.00}]

	// other
	fmt.Println(tree)
	// [[[<nil> {3.00 1.00} {1.00 8.00}] {5.00 0.00} [<nil> {8.00 3.00} {7.00 5.00}]]]
	fmt.Println(tree.Points())
	// [{3.00 1.00} {1.00 8.00} {5.00 0.00} {8.00 3.00} {7.00 5.00}]
}
```

### n-dimensional Points (`points.Point`)
```go
type Data struct {
	value string
}

func main() {
    tree := kdtree.New([]kdtree.Point{
    points.NewPoint([]float64{7, 2, 3}, Data{value: "first"}),
    points.NewPoint([]float64{3, 7, 10}, Data{value: "second"}),
    points.NewPoint([]float64{4, 6, 1}, Data{value: "third"}),
    })
    
    // Insert
    tree.Insert(points.NewPoint([]float64{12, 4, 6}, Data{value: "fourth"}))
    tree.Insert(points.NewPoint([]float64{8, 1, 0}, Data{value: "fifth"}))
    
    // KNN (k-nearest neighbor)
    fmt.Println(tree.KNN(&points.Point{Coordinates: []float64{1, 1, 1}}, 2))
    // [{[4 6 1] {third}} {[7 2 3] {first}}]
    
    // other
    fmt.Println(tree)
    // [[{[3 7 10] {second}} {[4 6 1] {third}} [{[8 1 0] {fifth}} {[7 2 3] {first}} {[12 4 6] {fourth}}]]]
    fmt.Println(tree.Points())
    // [{[3 7 10] {second}} {[4 6 1] {third}} {[8 1 0] {fifth}} {[7 2 3] {first}} {[12 4 6] {fourth}}]
}
```
