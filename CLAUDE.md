# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

### Core Development
- **Run the server**: `uv run yahoo_finance_mcp/server.py` - Starts the MCP server in development mode for testing with MCP Inspector
- **Install dependencies**: `uv pip install -e .` - Installs the project in editable mode
- **Setup environment**: `uv venv && source .venv/bin/activate` - Creates and activates virtual environment

### Code Quality
- **Format code**: `black yahoo_finance_mcp/` - Formats Python code using Black (line length: 100)
- **Sort imports**: `isort yahoo_finance_mcp/` - Sorts imports according to the Black profile
- **Type checking**: `mypy yahoo_finance_mcp/` - Runs type checking (strict mode enabled)
- **Security check**: `bandit yahoo_finance_mcp/` - Runs security analysis

## Project Architecture

### Core Components
This is a Model Context Protocol (MCP) server built with FastMCP that provides comprehensive financial data access through Yahoo Finance APIs.

#### Main Server (`yahoo_finance_mcp/server.py`)
- **FastMCP Server**: Single-file MCP server implementation using the FastMCP framework
- **Tool Registration**: All financial data tools are registered as async functions with the `@yfinance_server.tool` decorator
- **Data Models**: Uses Pydantic enums for type safety (FinancialType, HolderType, RecommendationType)
- **Error Handling**: Consistent error handling pattern across all tools with ticker validation

#### Data Categories
The server exposes 9 MCP tools organized into functional categories:

1. **Stock Information**: `get_historical_stock_prices`, `get_stock_info`, `get_yahoo_finance_news`, `get_stock_actions`
2. **Financial Statements**: `get_financial_statement` (supports income statement, balance sheet, cash flow - annual/quarterly)
3. **Holder Information**: `get_holder_info` (major holders, institutional, mutual funds, insider transactions)
4. **Options Data**: `get_option_expiration_dates`, `get_option_chain`
5. **Analyst Data**: `get_recommendations` (recommendations and upgrades/downgrades)

#### Key Design Patterns
- **Enum-based Configuration**: Uses string enums for financial statement types, holder types, and recommendation types
- **JSON Response Format**: All tools return JSON-serialized data for consistent integration
- **Ticker Validation**: Each tool validates ticker existence using `company.isin` before processing
- **Pandas Integration**: Extensive use of pandas for data transformation and JSON serialization
- **Date Handling**: Consistent ISO date formatting for time-series data

### Dependencies
- **Core**: `mcp[cli]>=1.6.0` for MCP server functionality
- **Data Source**: `yfinance>=0.2.62` for Yahoo Finance API access
- **Data Processing**: pandas (implicit dependency through yfinance) for data manipulation
- **Development**: Black, isort, mypy, bandit for code quality and security

### Configuration
- **Python Version**: Requires Python 3.11+
- **Package Manager**: Uses `uv` for dependency management and virtual environments
- **Code Style**: Black formatter with 100-character line length
- **Type Safety**: MyPy with strict type checking enabled
- **Import Organization**: isort with Black-compatible profile

### Integration Points
- **Claude Desktop**: Configured via `claude_desktop_config.json` with uv command
- **MCP Inspector**: Direct testing via `uv run server.py`
- **Smithery.ai**: Configured for stdio transport in `smithery.yaml`

### Error Handling Strategy
All tools follow a consistent error handling pattern:
1. Ticker validation using `company.isin` check
2. Exception catching for network/API errors
3. Graceful error messages returned as strings
4. Fallback handling for missing data (e.g., no news available)