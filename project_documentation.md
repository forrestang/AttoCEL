# Opening Range Breakout Trading Dashboard

## Project Overview

This project is a comprehensive web-based backtesting dashboard for testing an **Opening Range Breakout (ORB)** trading strategy on cryptocurrency perpetual futures. The strategy uses a martingale position sizing approach combined with session-based trading to target consistent returns.

## Core Trading Strategy

### Opening Range Breakout Concept
- **Session-based trading**: Each trading session has defined start/end times (e.g., every 4 hours)
- **Opening Range**: The first N minutes of each session (e.g., first 10 minutes) defines the trading range
- **Breakout logic**: Enter trades when price breaks above the opening range high (LONG) or below the opening range low (SHORT)
- **Goal**: Achieve +1R (1 risk unit) profit per session using a martingale progression

### Martingale Position Sizing
The strategy uses a specific trade sequencing approach:

1. **Trade 1**: Enter on first breakout of opening range (1R position size)
   - If target hit → Session complete (+1R profit)
   - If stop hit → Continue to Trade 2

2. **Trade 2**: Enter opposite direction at Trade 1's stop price (2R position size)
   - If target hit → Net result: -1R + 3R = +1R profit (session complete)
   - If stop hit → Continue to Trade 3

3. **Trade 3**: Enter opposite direction at Trade 2's stop price (4R position size)
   - If target hit → Net result: -1R -2R + 5R = +1R profit (session complete)
   - If stop hit → Continue to Trade 4

4. **Pattern continues**: 1R, 2R, 4R, 8R, 16R... until target hit or session ends

### Entry and Exit Rules

#### Entry Rules
- **Immediate execution**: Enter as soon as price breaks the opening range boundary
- **Chain trading**: Subsequent trades enter at the stop price of the previous trade
- **Direction alternation**: Long → Short → Long → Short...

#### Stop and Target Calculation
Two methods supported:

**Bar-Based Method**:
- Stop: Opening range boundary ± tick buffer
- Target: Entry ± (stop distance × R multiple)

**ATR-Based Method**:
- Stop: Entry ± (ATR × multiplier), minimum 1 tick
- Target: Entry ± (stop distance × R multiple)

#### Exit Priority
- **Conservative approach**: If both stop and target can be hit in the same bar, assume stop is hit first
- **Target requirement**: Price must exceed target by at least 1 tick to trigger
- **Session end**: Close any open position at session boundary

## Technical Implementation

### Data Architecture
The system uses dual-timeframe data approach:

1. **Chart Data**: Higher timeframe data (5m, 15m, 1h, 4h) for:
   - Visual display on charts
   - Opening range calculation
   - ATR calculation

2. **Backtest Data**: Lower timeframe data (1m, 3m, 5m) for:
   - Precise entry/exit timing
   - Granular price movement simulation

### HyperLiquid API Integration
- **Symbol Discovery**: Fetches available trading pairs from HyperLiquid
- **Historical Data**: Downloads OHLCV data with configurable date ranges
- **Rate Limiting**: Respects 5K bar limit per API call
- **Data Validation**: Ensures data integrity and coverage

### Backtesting Engine
Built as a JavaScript class with the following capabilities:

#### Session Management
- Generates trading sessions based on configurable start times
- Handles session boundaries and overlapping periods
- Validates data availability for each session

#### Trade Execution Simulation
- Simulates real-time price movement through historical data
- Implements precise entry/exit logic
- Tracks position sizing and P&L calculation
- Handles edge cases (gaps, session endings, etc.)

#### Statistical Analysis
Calculates comprehensive performance metrics:
- Session win rate
- Total return in R multiples
- Maximum drawdown
- Profit factor
- Sharpe ratio
- Trade sequence analysis (1st trade vs 2nd+ trade performance)

### Visualization Layer
Uses TradingView Lightweight Charts 5.0.8 for professional chart display:

#### Chart Features
- Candlestick price charts with volume overlay
- Trade entry/exit markers with color coding
- Opening range boundary visualization
- Session period highlighting
- Interactive chart controls

#### Trade Markers
- **Long entries**: Green arrows pointing up
- **Short entries**: Red arrows pointing down
- **Exits**: Color-coded by profitability
- **Hover details**: Price, P&L, duration information

### Advanced Filtering System
Post-backtest analysis with multiple filter dimensions:

#### Time-Based Filters
- **Day of week exclusion**: Remove trades on specific days
- **Time window filtering**: Include/exclude specific UTC hours
- **Date range filtering**: Focus on specific historical periods

#### Trade-Based Filters
- **Trade sequence filtering**: Analyze only 1st trades, 2nd+ trades, etc.
- **Maximum trades per session**: Limit analysis to first N trades
- **Specific trade exclusions**: Remove individual trades or sessions

#### Market Condition Filters
- **Volatility filtering**: ATR-based market regime analysis
- **Volume filtering**: Exclude low-volume periods
- **Event filtering**: Remove trades during major news events

### Parameter Optimization
Grid search optimization across multiple dimensions:

#### Optimizable Parameters
- Session length (180m, 240m, 360m, 480m)
- Opening range duration (5m, 10m, 15m, 20m)
- Stop/target method (bar-based vs ATR-based)
- Target R multiples (1.0, 1.5, 2.0)
- ATR periods and multipliers

#### Optimization Scoring
Weighted scoring system considering:
- Session win rate (40% weight)
- Profit factor (30% weight)
- Sharpe ratio (20% weight)
- Drawdown penalty (10% weight)

## User Interface Design

### Dashboard Layout
- **Left sidebar**: Controls and configuration
- **Top section**: Price charts with trade visualization
- **Bottom section**: Results tables and analysis

### Control Panels

#### Data Source Panel
- HyperLiquid API integration vs file upload
- Asset selection from available symbols
- Dual timeframe configuration
- Date range selection with size estimation

#### Strategy Configuration Panel
- Session timing parameters
- Stop/target method selection
- Risk management settings
- Martingale progression customization

#### Analysis Panel
- Real-time statistics display
- Trade log with sorting/filtering
- Session summary analysis
- Parameter optimization results

### Export Capabilities
- **CSV export**: Complete trade logs for external analysis
- **Configuration export**: Save/load strategy parameters
- **Chart export**: PNG screenshots of results

## File Structure and Dependencies

### Single File Architecture
The dashboard is built as a single HTML file containing:
- Complete CSS styling (dark theme, responsive design)
- JavaScript backtesting engine
- HyperLiquid API integration
- TradingView Lightweight Charts 5.0.8
- All UI components and event handlers

### External Dependencies
- **TradingView Lightweight Charts 5.0.8**: Professional charting library
- **HyperLiquid API**: Real-time cryptocurrency data source

### Browser Compatibility
- **Recommended**: Chrome, Edge (Chromium-based browsers)
- **Supported**: Firefox, Safari
- **Requirements**: Modern ES6+ JavaScript support

## Usage Workflow

### 1. Data Acquisition
1. Select cryptocurrency asset (BTC, ETH, SOL, etc.)
2. Configure chart timeframe (for display) and backtest timeframe (for execution)
3. Set date range for historical analysis
4. Download data from HyperLiquid API

### 2. Strategy Configuration
1. Set session parameters (length, opening range duration, start times)
2. Choose stop/target calculation method (bar-based or ATR-based)
3. Configure risk management (position sizes, martingale progression)
4. Set target R multiple for profit-taking

### 3. Backtesting Execution
1. Run complete backtest across all historical sessions
2. Review real-time performance statistics
3. Analyze trade-by-trade results in detailed logs
4. Examine chart visualization with trade markers

### 4. Results Analysis
1. Apply post-analysis filters to explore different scenarios
2. Analyze performance by trade sequence, time periods, market conditions
3. Compare different parameter combinations
4. Export results for further analysis

### 5. Optimization
1. Run parameter grid search across multiple configurations
2. Review top-performing parameter combinations
3. Analyze trade-offs between different optimization objectives
4. Select optimal parameters for live trading consideration

## Key Performance Considerations

### Computational Efficiency
- Efficient session generation and data filtering
- Minimal memory allocation during backtesting
- Progress tracking for long-running operations
- Responsive UI updates during processing

### Data Management
- Local browser storage for downloaded data
- Automatic file naming and organization
- Data validation and integrity checking
- Memory-conscious data structures

### Accuracy and Precision
- Precise timestamp handling for entry/exit timing
- Conservative bias in execution simulation
- Proper handling of edge cases and missing data
- Validation against known market behavior

## Future Enhancement Opportunities

### Additional Features
- **Multiple asset backtesting**: Portfolio-level analysis
- **Live trading integration**: Paper trading capabilities
- **Advanced order types**: Stop-limit orders, trailing stops
- **Risk management**: Portfolio heat maps, correlation analysis

### Performance Improvements
- **WebWorker integration**: Background processing for large datasets
- **Data caching**: Local storage for frequently used datasets
- **Streaming updates**: Real-time data integration
- **Cloud storage**: Remote configuration and results storage

### Analysis Enhancements
- **Monte Carlo simulation**: Stress testing strategy robustness
- **Walk-forward analysis**: Out-of-sample validation
- **Machine learning integration**: Pattern recognition and optimization
- **Statistical significance testing**: Confidence intervals and p-values

## Technical Notes for Development

### Code Organization
The codebase is structured in logical sections:
- **Part 1**: HTML structure and CSS styling
- **Part 2**: Form controls and user interface logic
- **Part 3**: HyperLiquid API integration and data management
- **Part 4**: TradingView Lightweight Charts integration
- **Part 5**: Backtesting engine and results processing

### Error Handling
Comprehensive error handling throughout:
- API failures with fallback options
- Data validation with user feedback
- Graceful degradation for missing features
- Clear error messages and recovery suggestions

### Browser Storage Limitations
- **No localStorage/sessionStorage**: Uses in-memory storage only
- **File download/upload**: Browser-native file operations
- **JSON data format**: Human-readable configuration and results
- **Memory management**: Efficient data structures for large datasets

### Performance Optimization
- **Minimal DOM manipulation**: Batch updates for better performance
- **Efficient algorithms**: Optimized loops and data structures
- **Progress indicators**: User feedback for long operations
- **Lazy loading**: Chart and data loading on demand

This documentation provides complete context for understanding the Opening Range Breakout Trading Dashboard project, its implementation, and usage patterns. The system represents a sophisticated approach to systematic cryptocurrency trading strategy development and validation.