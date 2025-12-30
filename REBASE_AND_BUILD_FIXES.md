# Rebase and Build Fixes Documentation

This document details the fixes applied to resolve compilation errors after rebasing the `breed` branch.

## Overview

After rebasing the `breed` branch onto the latest `master`, several compilation errors occurred due to merge conflicts and missing implementations. This document outlines each issue and its solution.

---

## Issues and Solutions

### 1. Protocol Error: Empty Packet for `AcCmdCRBreedingFailureCardChoose`

**Error:**
```
Couldn't read 2 bytes from the buffer (cursor: 0, available: 0). Not enough space.
```

**Cause:** The server expected to read an `int16_t statusOrFlag` field, but the client sends an empty packet.

**Solution:** Modified the `Read()` method to not attempt reading any data:

**File:** `src/libserver/network/command/proto/RanchMessageDefinitions.cpp`

```cpp
void AcCmdCRBreedingFailureCardChoose::Read(
  AcCmdCRBreedingFailureCardChoose& command,
  SourceStream& stream)
{
  // Client sends empty packet - no data to read
  // stream.Read(command.statusOrFlag);
}
```

**Commit:** `7008f30` - Fix AcCmdCRBreedingFailureCardChoose protocol - client sends empty packet

---

### 2. Missing Function: `GetFileDataSource()`

**Error:**
```
undefined reference to `server::DataDirector::GetFileDataSource()'
```

**Cause:** Function was declared in the header but not implemented in the source file.

**Solution:** Added implementation to return the casted data source:

**File:** `src/libserver/data/DataDirector.cpp`

```cpp
FileDataSource& DataDirector::GetFileDataSource()
{
  return static_cast<FileDataSource&>(*_primaryDataSource);
}
```

**Commit:** `60b8bf5` - Add missing GetFileDataSource implementation

---

### 3. Duplicate Field Declaration: `fatigue` in Horse Struct

**Error:**
```
error: redeclaration of 'dao::Field<uint16_t> server::data::Horse::fatigue'
```

**Cause:** The `fatigue` field was declared twice with different types:
- Line 395: `dao::Field<uint32_t> fatigue{0u};`
- Line 405: `dao::Field<uint16_t> fatigue{0u};` (duplicate)

**Solution:** Removed the duplicate `uint16_t` declaration on line 405.

**File:** `include/libserver/data/DataDefinitions.hpp`

```cpp
// Removed this line:
// dao::Field<uint16_t> fatigue{0u};
```

**Commit:** `daf7e0e` - Fix duplicate fatigue field declaration in Horse struct

---

### 4. Lambda Capture Error: Missing Variables

**Error:**
```
error: 'rolledBonusType' is not captured
error: 'rolledBonusId' is not captured
```

**Cause:** Lambda function used `rolledBonusType` and `rolledBonusId` without capturing them.

**Solution:** Added variables to the lambda capture list:

**File:** `src/server/ranch/RanchDirector.cpp`

```cpp
// Before:
foalRecord.Mutable([this, &command, &mareRecord, &stallionRecord, &foalUid, &foalTid, 
                    &foalPotentialType, &foalParts, &foalAppearance, &foalStats](data::Horse& foal)

// After:
foalRecord.Mutable([this, &command, &mareRecord, &stallionRecord, &foalUid, &foalTid, 
                    &foalPotentialType, &foalParts, &foalAppearance, &foalStats,
                    rolledBonusId, rolledBonusType](data::Horse& foal)
```

**Commit:** `03495e2` - Fix lambda capture: add rolledBonusId and rolledBonusType to capture list

---

### 5. Missing Stallion Cache Methods

**Error:**
```
undefined reference to `server::DataDirector::GetStallion(unsigned long)'
undefined reference to `server::DataDirector::CreateStallion()'
undefined reference to `server::DataDirector::GetStallionCache()'
```

**Cause:** Methods were declared in the header but not implemented.

**Solution:** Added implementations for all three methods:

**File:** `src/libserver/data/DataDirector.cpp`

```cpp
Record<data::Stallion> DataDirector::GetStallion(data::Uid stallionUid) noexcept
{
  if (stallionUid == data::InvalidUid)
    return {};
  return _stallionStorage.Get(stallionUid).value_or(Record<data::Stallion>{});
}

Record<data::Stallion> DataDirector::CreateStallion() noexcept
{
  try
  {
    return _stallionStorage.Create(
      [this]()
      {
        data::Stallion stallion;
        _primaryDataSource->CreateStallion(stallion);

        return std::make_pair(stallion.uid(), std::move(stallion));
      });
  }
  catch (const std::exception& x)
  {
    spdlog::error("Exception while creating a stallion record on the primary data source: {}", x.what());
    return {};
  }
}

DataDirector::StallionStorage& DataDirector::GetStallionCache()
{
  return _stallionStorage;
}
```

**Commit:** `265a067` - Add missing stallion cache methods (GetStallion, CreateStallion, GetStallionCache)

---

## GitHub Actions Setup

To enable automatic Docker image builds for the `breed` branch, the workflow was updated:

**File:** `.github/workflows/docker-publish.yml`

```yaml
on:
  push:
    branches: [ "master", "breed" ]  # Added "breed" branch
```

**Commit:** `6397e30` - Add breed branch to Docker build workflow

---

## Summary

All fixes have been applied and the build now succeeds. The Docker image is available at:

```
ghcr.io/khemzal/alicia-server:breed
```

### Key Takeaways

1. **Protocol Mismatches:** Always verify client-server protocol expectations match
2. **Merge Conflicts:** Check for duplicate declarations after rebasing
3. **Lambda Captures:** Ensure all used variables are captured in lambda expressions
4. **Missing Implementations:** Verify all declared functions have corresponding implementations
5. **Build Verification:** Use CI/CD to catch compilation errors early

---

## Related Files

- `src/libserver/network/command/proto/RanchMessageDefinitions.cpp`
- `src/libserver/data/DataDirector.cpp`
- `include/libserver/data/DataDefinitions.hpp`
- `src/server/ranch/RanchDirector.cpp`
- `.github/workflows/docker-publish.yml`

---

*Documentation created: December 30, 2025*
