```go
package curveAave

import (
	"math/big"
	"testing"

	"github.com/KyberNetwork/router-service/internal/pkg/utils"
	"github.com/holiman/uint256"
	"github.com/stretchr/testify/assert"
)

var (
	tokenIndexFrom = 0
	tokenIndexTo   = 2

	// uint256
	balances = []*uint256.Int{
		uint256.MustFromDecimal("70382246141748845587674511"),
		uint256.MustFromDecimal("164292114057107"),
		uint256.MustFromDecimal("205974869965084"),
	}
	tokenPrecisionMultipliers = []*uint256.Int{
		uint256.MustFromDecimal("1"),
		uint256.MustFromDecimal("1000000000000"),
		uint256.MustFromDecimal("1000000000000"),
	}
	dx = uint256.MustFromDecimal("1234567890000000000")

	// big.Int
	balancesB = []*big.Int{
		utils.NewBig10("70382246141748845587674511"),
		utils.NewBig10("164292114057107"),
		utils.NewBig10("205974869965084"),
	}
	tokenPrecisionMultipliersB = []*big.Int{
		utils.NewBig10("1"),
		utils.NewBig10("1000000000000"),
		utils.NewBig10("1000000000000"),
	}
	dxB = utils.NewBig10("1234567890000000000")
)

func BenchmarkGetDyUnderlying(b *testing.B) {
	for i := 0; i < b.N; i++ {
		GetDyUnderlying(
			balances,
			tokenPrecisionMultipliers,
			1621013782,
			uint256.NewInt(200000),
			1620408998,
			uint256.NewInt(100000),
			uint256.NewInt(3000000),
			uint256.NewInt(20000000000),
			tokenIndexFrom,
			tokenIndexTo,
			dx)
	}
}

func BenchmarkO_GetDyUnderlying(b *testing.B) {
	for i := 0; i < b.N; i++ {
		O_GetDyUnderlying(
			balancesB,
			tokenPrecisionMultipliersB,
			1621013782,
			big.NewInt(200000),
			1620408998,
			big.NewInt(100000),
			big.NewInt(3000000),
			big.NewInt(20000000000),
			tokenIndexFrom,
			tokenIndexTo,
			dxB)
	}
}

func TestGetDyUnderlying(t *testing.T) {
	dy, fee, err := GetDyUnderlying(
		balances,
		tokenPrecisionMultipliers,
		1621013782,
		uint256.NewInt(200000),
		1620408998,
		uint256.NewInt(100000),
		uint256.NewInt(3000000),
		uint256.NewInt(20000000000),
		tokenIndexFrom,
		tokenIndexTo,
		dx)

	dyB, feeB, errB := O_GetDyUnderlying(
		balancesB,
		tokenPrecisionMultipliersB,
		1621013782,
		big.NewInt(200000),
		1620408998,
		big.NewInt(100000),
		big.NewInt(3000000),
		big.NewInt(20000000000),
		tokenIndexFrom,
		tokenIndexTo,
		dxB)

	if assert.NoError(t, err) && assert.NoError(t, errB) {
		assert.Equal(t, dy.ToBig(), dyB)
		assert.Equal(t, fee.ToBig(), feeB)
	}
}

```
```bash
go test -v ./internal/pkg/core/curve-aave -bench=. -benchmem
=== RUN   TestGetDyUnderlying
--- PASS: TestGetDyUnderlying (0.00s)
goos: darwin
goarch: arm64
pkg: github.com/KyberNetwork/router-service/internal/pkg/core/curve-aave
BenchmarkGetDyUnderlying
BenchmarkGetDyUnderlying-10               379944              3040 ns/op            2840 B/op         89 allocs/op
BenchmarkO_GetDyUnderlying
BenchmarkO_GetDyUnderlying-10             113070             10734 ns/op           14967 B/op        321 allocs/op
PASS
ok      github.com/KyberNetwork/router-service/internal/pkg/core/curve-aave     2.614s     3.543s
```
