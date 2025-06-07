# Primary Key Support - Implementation Plan

## Overview

This plan breaks down the primary key support implementation into actionable tasks, organized by phases. Tasks should be completed from top to bottom within each phase.

---

## Phase 1: Core Infrastructure & Type System

### 1.1 Update Type Definitions

- [x] **Task 1.1.1**: Remove `primary?: boolean` field from `Column` interface in `src/types/schema.ts`
- [x] **Task 1.1.2**: Add `PrimaryKeyConstraint` interface with `name?: string` and `columns: string[]` fields
- [x] **Task 1.1.3**: Add `primaryKey?: PrimaryKeyConstraint` field to `Table` interface
- [x] **Task 1.1.4**: Update all imports and references to use the new type definitions

### 1.2 Update SQL Utilities

- [x] **Task 1.2.1**: Update `generateCreateTableStatement()` in `src/utils/sql.ts` to use `Table.primaryKey` instead of `Column.primary`
- [x] **Task 1.2.2**: Add logic to generate both single-column and composite primary key SQL
- [x] **Task 1.2.3**: Add support for named primary key constraints in SQL generation
- [x] **Task 1.2.4**: Remove column-level `PRIMARY KEY` generation from column definitions
- [x] **Task 1.2.5**: Update `columnsAreDifferent()` function to remove primary key comparison logic

### 1.3 Update Schema Parser

- [x] **Task 1.3.1**: Add method to extract table-level primary key constraints from CST
- [x] **Task 1.3.2**: Add method to extract named primary key constraints (`CONSTRAINT pk_name PRIMARY KEY (...)`)
- [x] **Task 1.3.3**: Convert column-level `PRIMARY KEY` to table-level `PrimaryKeyConstraint` representation
- [x] **Task 1.3.4**: Add validation to ensure only one primary key definition exists per table
- [x] **Task 1.3.5**: Update `parseCreateTableFromCST()` to build `Table.primaryKey` field
- [x] **Task 1.3.6**: Remove primary key flag setting in column parsing logic

### 1.4 Update Database Inspector

- [x] **Task 1.4.1**: Replace current primary key detection query with composite-aware query
- [x] **Task 1.4.2**: Extract primary key constraint names from `information_schema.table_constraints`
- [x] **Task 1.4.3**: Build `PrimaryKeyConstraint` objects from query results with proper column ordering
- [x] **Task 1.4.4**: Remove column-level primary key flag setting in inspector
- [x] **Task 1.4.5**: Update `getCurrentSchema()` to populate `Table.primaryKey` field

---

## Phase 2: Schema Differ & SQL Generation

### 2.1 Primary Key Comparison Logic

- [ ] **Task 2.1.1**: Add `comparePrimaryKeys()` method to compare `PrimaryKeyConstraint` objects
- [ ] **Task 2.1.2**: Add primary key change detection in `generateMigrationPlan()`
- [ ] **Task 2.1.3**: Identify three scenarios: add PK, drop PK, modify PK
- [ ] **Task 2.1.4**: Add constraint name resolution logic (auto-generate vs use existing)

### 2.2 Primary Key SQL Generation

- [ ] **Task 2.2.1**: Add `generateAddPrimaryKeySQL()` method for `ADD CONSTRAINT` statements
- [ ] **Task 2.2.2**: Add `generateDropPrimaryKeySQL()` method for `DROP CONSTRAINT` statements
- [ ] **Task 2.2.3**: Add logic to generate constraint names automatically (`pk_tablename` pattern)
- [ ] **Task 2.2.4**: Handle single-column vs composite primary key SQL generation
- [ ] **Task 2.2.5**: Add support for named constraints in SQL generation

### 2.3 Migration Plan Integration

- [ ] **Task 2.3.1**: Integrate primary key changes into existing `generateMigrationPlan()` flow
- [ ] **Task 2.3.2**: Add proper operation ordering (drop PK before modify, add PK after column changes)
- [ ] **Task 2.3.3**: Handle primary key changes alongside table and column modifications
- [ ] **Task 2.3.4**: Add primary key change statements to migration plan output

---

## Phase 3: Data Validation & Safety

### 3.1 Pre-Migration Validation

- [ ] **Task 3.1.1**: Add `validatePrimaryKeyData()` method to check for duplicates
- [ ] **Task 3.1.2**: Add NULL value detection in primary key columns
- [ ] **Task 3.1.3**: Add validation that runs before primary key addition
- [ ] **Task 3.1.4**: Generate clear error messages with row counts and examples
- [ ] **Task 3.1.5**: Add suggestions for resolving data conflicts

### 3.2 Conflict Detection & Resolution

- [ ] **Task 3.2.1**: Detect existing unique constraints that conflict with new primary keys
- [ ] **Task 3.2.2**: Check for existing indexes on primary key columns
- [ ] **Task 3.2.3**: Add pre-migration validation in `plan` command
- [ ] **Task 3.2.4**: Display validation results and warnings in plan output
- [ ] **Task 3.2.5**: Implement safe-fail behavior when conflicts are detected

### 3.3 Error Handling & User Experience

- [ ] **Task 3.3.1**: Add structured error types for primary key validation failures
- [ ] **Task 3.3.2**: Implement user-friendly error messages with actionable suggestions
- [ ] **Task 3.3.3**: Add error recovery guidance for common scenarios
- [ ] **Task 3.3.4**: Update CLI output to show primary key change summaries
- [ ] **Task 3.3.5**: Add progress indicators for primary key operations

---

## Phase 4: Testing & Polish

### 4.1 Unit Tests

- [ ] **Task 4.1.1**: Create `src/test/columns/constraints/primary-keys.test.ts`
- [ ] **Task 4.1.2**: Add parser tests for column-level primary key syntax
- [ ] **Task 4.1.3**: Add parser tests for table-level primary key syntax (single & composite)
- [ ] **Task 4.1.4**: Add parser tests for named primary key constraints
- [ ] **Task 4.1.5**: Add inspector tests for primary key detection from database
- [ ] **Task 4.1.6**: Add differ tests for primary key change detection
- [ ] **Task 4.1.7**: Add SQL generation tests for all primary key scenarios

### 4.2 Integration Tests

- [ ] **Task 4.2.1**: Add end-to-end test for creating table with single primary key
- [ ] **Task 4.2.2**: Add end-to-end test for creating table with composite primary key
- [ ] **Task 4.2.3**: Add end-to-end test for adding primary key to existing table
- [ ] **Task 4.2.4**: Add end-to-end test for changing primary key columns
- [ ] **Task 4.2.5**: Add end-to-end test for removing primary key from table
- [ ] **Task 4.2.6**: Add test for primary key operations with existing data

### 4.3 Data Integrity & Edge Case Tests

- [ ] **Task 4.3.1**: Add tests for duplicate data detection and error handling
- [ ] **Task 4.3.2**: Add tests for NULL value detection in primary key columns
- [ ] **Task 4.3.3**: Add tests for conflicting unique constraints
- [ ] **Task 4.3.4**: Add tests for named vs auto-generated constraint handling
- [ ] **Task 4.3.5**: Add tests for large dataset primary key operations
- [ ] **Task 4.3.6**: Add tests for concurrent primary key operations

### 4.4 Documentation & Examples

- [ ] **Task 4.4.1**: Update README.md with primary key examples
- [ ] **Task 4.4.2**: Add primary key migration examples to documentation
- [ ] **Task 4.4.3**: Document error scenarios and resolution steps
- [ ] **Task 4.4.4**: Add troubleshooting guide for primary key conflicts
- [ ] **Task 4.4.5**: Update schema.sql example with composite primary key examples

---

## Dependencies & Prerequisites

### Before Starting Phase 1:

- Ensure all existing tests pass
- Understand current `SERIAL PRIMARY KEY` usage patterns
- Review existing schema examples and test data

### Before Starting Phase 2:

- Phase 1 must be complete and tested
- All type definitions updated and working
- Parser and inspector generating correct `Table.primaryKey` objects

### Before Starting Phase 3:

- Phase 2 must be complete and tested
- Basic primary key SQL generation working
- Migration planning including primary key changes

### Before Starting Phase 4:

- Core functionality complete and working
- All phases 1-3 implemented and manually tested
- Ready for comprehensive test coverage

---

## Success Criteria

### Phase 1 Complete When:

- [x] All type definitions updated without breaking existing functionality
- [x] Parser correctly converts all primary key syntax to `Table.primaryKey` format
- [x] Inspector correctly detects and represents primary keys from database
- [x] SQL generation works for both single and composite primary keys

### Phase 2 Complete When:

- [ ] Schema differ correctly detects all primary key changes
- [ ] SQL generation produces correct `ALTER TABLE` statements
- [ ] Migration planning includes primary key operations in correct order
- [ ] All primary key change scenarios generate valid SQL

### Phase 3 Complete When:

- [ ] Data validation prevents invalid primary key operations
- [ ] Clear error messages guide users through conflict resolution
- [ ] Primary key operations fail safely when data conflicts exist
- [ ] Users can understand and resolve primary key issues independently

### Phase 4 Complete When:

- [ ] > 95% test coverage for primary key functionality
- [ ] All edge cases covered with appropriate tests
- [ ] Documentation complete with examples and troubleshooting
- [ ] Feature ready for production use

---

## Estimated Timeline

- **Phase 1**: 3-4 days (Core infrastructure changes)
- **Phase 2**: 2-3 days (Differ logic and SQL generation)
- **Phase 3**: 2-3 days (Validation and error handling)
- **Phase 4**: 3-4 days (Testing and documentation)

**Total Estimated Time**: 10-14 days

---

## Notes

- Each task should be implemented and tested before moving to the next
- Manual testing should be done after each phase before proceeding
- Consider creating a test database with various primary key scenarios for validation
- Keep commits small and focused on individual tasks for easier review and rollback
