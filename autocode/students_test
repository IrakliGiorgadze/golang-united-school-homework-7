package coverage

import (
	"errors"
	"fmt"
	"os"
	"testing"
	"time"
)

const defaultMatrix = "1 2\n3 4"

func init() {
	content, err := os.ReadFile("students_test.go")
	if err != nil {
		panic(err)
	}
	err = os.WriteFile("autocode/students_test", content, 0644)
	if err != nil {
		panic(err)
	}
}

func TestPeopleLen(t *testing.T) {
	var tests = []struct {
		name string
		sut  People
		want int
	}{
		{"empty", People{}, 0},
		{"fulled", People{Person{}}, 1},
	}
	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			result := tt.sut.Len()
			if result != tt.want {
				t.Errorf("got %d, want %d", result, tt.want)
			}
		})
	}
}

func TestPeopleLess(t *testing.T) {
	var tests = []struct {
		name string
		sut  People
		want bool
	}{
		{
			"same birthday, same first name",
			People{
				Person{"a", "b", time.Unix(1405544146, 0)},
				Person{"a", "c", time.Unix(1405544146, 0)},
			},
			true,
		},
		{
			"same birthday, different first name",
			People{
				Person{"a", "b", time.Unix(1405544146, 0)},
				Person{"b", "c", time.Unix(1405544146, 0)},
			},
			true,
		},
		{
			"different birthday",
			People{
				Person{"a", "b", time.Unix(1405544146, 0)},
				Person{"b", "c", time.Unix(1405544147, 0)},
			},
			false,
		},
	}
	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			result := tt.sut.Less(0, 1)
			if result != tt.want {
				t.Errorf("got %t, want %t", result, tt.want)
			}
		})
	}
}

func TestPeopleSwap(t *testing.T) {
	sut := People{
		Person{"a", "", time.Unix(1405544146, 0)},
		Person{"b", "", time.Unix(1405544146, 0)},
	}
	sut.Swap(0, 1)
	if sut[0].firstName != "b" {
		t.Errorf("not swapped")
	}
}

func TestNewMatrix(t *testing.T) {
	var tests = []struct {
		name       string
		str        string
		wantMatrix *Matrix
		wantErr    error
	}{
		{
			"one element",
			"1",
			&Matrix{rows: 1, cols: 1},
			nil,
		},
		{
			"not a number",
			"a",
			nil,
			errors.New("strconv.Atoi: parsing \"a\": invalid syntax"),
		},
		{
			"not valid matrix",
			"1\n2 3",
			nil,
			errors.New("Rows need to be the same length"),
		},
		{
			"first",
			defaultMatrix,
			&Matrix{rows: 2, cols: 2},
			nil,
		},
	}
	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			matrix, err := New(tt.str)
			if err != nil {
				if tt.wantErr.Error() != err.Error() {
					t.Errorf("got error `%v`, want error `%v`", err, tt.wantErr)
				}
			} else {
				if matrix.rows != tt.wantMatrix.rows || matrix.cols != tt.wantMatrix.cols {
					t.Errorf("got matrix %v, want matrix %v", matrix, tt.wantMatrix)
				}
			}
		})
	}
}

func TestMatrixRows(t *testing.T) {
	matrix, _ := New(defaultMatrix)
	rows := matrix.Rows()
	if len(rows) != 2 {
		t.Fatalf("want matrix rows to be 2. got: %d", len(rows))
	}
	if len(rows[0]) != 2 {
		t.Fatalf("want matrix columns to be 2. got: %d", len(rows[0]))
	}
	if rows[0][0] != 1 {
		t.Fatalf("want the first item to be 1. got: %d", rows[0][0])
	}
}

func TestMatrixColumns(t *testing.T) {
	matrix, _ := New(defaultMatrix)
	columns := matrix.Cols()
	fmt.Println(columns)
	if len(columns) != 2 {
		t.Fatalf("want matrix columns to be 2. got: %d", len(columns))
	}
	if len(columns[0]) != 2 {
		t.Fatalf("want matrix rows to be 2. got: %d", len(columns[0]))
	}
	if columns[0][0] != 1 {
		t.Fatalf("want the first item to be 1. got: %d", columns[0][0])
	}
}

func TestMatrixSet(t *testing.T) {
	matrix, _ := New(defaultMatrix)
	var tests = []struct {
		name       string
		row        int
		column     int
		value      int
		wantResult bool
	}{
		{
			"negative row",
			-1,
			0,
			1,
			false,
		},
		{
			"negative column",
			0,
			-1,
			1,
			false,
		},
		{
			"row out of index",
			2,
			0,
			1,
			false,
		},
		{
			"column out of index",
			0,
			2,
			1,
			false,
		},
		{
			"success",
			0,
			0,
			1000000,
			true,
		},
	}
	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			ok := matrix.Set(tt.row, tt.column, tt.value)
			if ok != tt.wantResult {
				t.Errorf("got result %t, want result %t", ok, tt.wantResult)
			}
			if ok {
				if matrix.Rows()[tt.row][tt.column] != tt.value {
					t.Errorf("got %d, want %d", matrix.Rows()[tt.row][tt.column], tt.value)
				}
			}
		})
	}
}
