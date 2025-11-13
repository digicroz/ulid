# @digicroz/ulid

> **TypeScript ULID library with monotonic generation and binary conversion** - Universally Unique Lexicographically Sortable Identifiers for JavaScript and TypeScript projects.

[![npm version](https://badge.fury.io/js/@digicroz/ulid.svg)](https://www.npmjs.com/package/@digicroz/ulid)
[![TypeScript](https://img.shields.io/badge/TypeScript-Ready-blue.svg)](https://www.typescriptlang.org/)
[![MIT License](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)
[![Tree Shakable](https://img.shields.io/badge/Tree--Shakable-✓-brightgreen.svg)](https://webpack.js.org/guides/tree-shaking/)

A TypeScript library for generating and manipulating ULIDs (Universally Unique Lexicographically Sortable Identifiers).

## 🌐 Environment Compatibility

This library is designed to work across multiple JavaScript environments:

- **✅ Node.js** - Server-side applications
- **✅ Browser** - Client-side web applications
- **✅ Web Workers** - Background processing
- **✅ React Native** - Mobile applications

## What is ULID?

ULID (Universally Unique Lexicographically Sortable Identifier) is a 128-bit identifier that is:

- **Sortable** - Lexicographically sortable by time
- **Compact** - 26 characters (vs 36 for UUID)
- **URL-safe** - Uses Crockford's Base32 alphabet
- **Case-insensitive** - No ambiguous characters
- **Monotonic** - Guaranteed ordering within the same millisecond
- **Time-based** - Encodes a timestamp in the first 48 bits

## Features

- 🚀 **TypeScript Support** - Full TypeScript support with type definitions
- 📦 **Tree Shakable** - Import only what you need
- 🔧 **Monotonic Generation** - Ensures IDs are always increasing
- 💾 **Binary Conversion** - Lossless conversion between string and binary formats
- ✅ **Validation** - Built-in ULID format validation
- ⏰ **Time Decoding** - Extract timestamps from ULIDs
- 🌐 **Cross-Platform** - Works in Node.js, browsers, and web workers
- 💡 **Excellent IDE Support** - Full auto-completion and IntelliSense support

## Installation

```bash
npm install @digicroz/ulid
```

**Alternative package managers:**

```bash
# Yarn
yarn add @digicroz/ulid

# pnpm
pnpm add @digicroz/ulid

# Bun
bun add @digicroz/ulid
```

## Quick Start

```typescript
import { generateUlid, isValidUlid, decodeTimeFromUlid } from "@digicroz/ulid";

// Generate a new ULID
const id = generateUlid();
console.log(id); // "01JCQX5YQHJ8Z9KQXH5YQHJ8Z0"

// Validate ULID format
const isValid = isValidUlid(id);
console.log(isValid); // true

// Decode timestamp from ULID
const timestamp = decodeTimeFromUlid(id);
console.log(new Date(timestamp)); // Current date/time
```

## API Reference

### Core Functions

#### `generateUlid(seedTime?: number): string`

Generates a new ULID with monotonic guarantees.

```typescript
import { generateUlid } from "@digicroz/ulid";

// Generate with current time
const id1 = generateUlid();

// Generate with specific timestamp
const id2 = generateUlid(Date.now());
```

**Features:**

- Monotonic mode ensures IDs are always increasing
- Last character is always '0' for perfect 128-bit binary conversion
- Handles clock drift gracefully

#### `isValidUlid(id: string): boolean`

Validates if a string is a valid ULID format.

```typescript
import { isValidUlid } from "@digicroz/ulid";

isValidUlid("01JCQX5YQHJ8Z9KQXH5YQHJ8Z0"); // true
isValidUlid("invalid"); // false
isValidUlid("01JCQX5YQHJ8Z9KQXH5YQHJ8ZI"); // false (invalid character 'I')
```

**Validation checks:**

- Exactly 26 characters
- All characters are valid Crockford Base32 (0-9, A-Z excluding I, L, O, U)
- Case-insensitive

#### `decodeTimeFromUlid(id: string): number`

Extracts the timestamp from a ULID.

```typescript
import { decodeTimeFromUlid } from "@digicroz/ulid";

const id = "01JCQX5YQHJ8Z9KQXH5YQHJ8Z0";
const timestamp = decodeTimeFromUlid(id);
console.log(new Date(timestamp)); // Date when the ULID was created

// Throws error if invalid ULID
try {
  decodeTimeFromUlid("invalid");
} catch (error) {
  console.error("Invalid ULID format");
}
```

#### `getUlidAge(id: string): number`

Returns the age of a ULID in milliseconds.

```typescript
import { getUlidAge } from "@digicroz/ulid";

const id = generateUlid();
// ... some time passes ...
const age = getUlidAge(id);
console.log(`ULID is ${age}ms old`);
```

#### `parseUlid(id: string): object`

Parses a ULID and extracts all its components.

```typescript
import { parseUlid } from "@digicroz/ulid";

const id = "01JCQX5YQHJ8Z9KQXH5YQHJ8Z0";
const parsed = parseUlid(id);

console.log(parsed);
// {
//   timestamp: 1699891200000,          // milliseconds since epoch
//   timestampSeconds: 1699891200,      // seconds since epoch
//   age: 1234                          // milliseconds since creation
// }
```

### Binary Conversion Functions

#### `ulidToBinary(id: string): Uint8Array`

Converts a ULID string to 16-byte binary format for efficient storage.

```typescript
import { ulidToBinary } from "@digicroz/ulid";

const id = "01JCQX5YQHJ8Z9KQXH5YQHJ8Z0";
const binary = ulidToBinary(id);
console.log(binary); // Uint8Array(16) [...]
console.log(binary.length); // 16 bytes (128 bits)
```

**Benefits:**

- Lossless conversion (100% reversible)
- Saves space in databases (16 bytes vs 26 characters)
- Perfect for binary storage formats

#### `binaryToUlid(bytes: Uint8Array | Buffer): string`

Converts 16-byte binary format back to ULID string.

```typescript
import { binaryToUlid, ulidToBinary } from "@digicroz/ulid";

const id = "01JCQX5YQHJ8Z9KQXH5YQHJ8Z0";
const binary = ulidToBinary(id);
const restored = binaryToUlid(binary);

console.log(id === restored); // true (lossless conversion)
```

**Features:**

- Works with both `Uint8Array` and Node.js `Buffer`
- Maintains consistency with generated ULIDs (last char is '0')

## Usage Examples

### Database Storage

Store ULIDs efficiently in binary format:

```typescript
import { generateUlid, ulidToBinary, binaryToUlid } from "@digicroz/ulid";

// Generate ULID
const userId = generateUlid();

// Store in database as binary (saves space)
const binaryId = ulidToBinary(userId);
await db.users.insert({ id: binaryId, name: "John" });

// Retrieve and convert back
const user = await db.users.findOne({ id: binaryId });
const readableId = binaryToUlid(user.id);
console.log(readableId); // Original ULID string
```

### Sorting by Creation Time

ULIDs are naturally sortable by creation time:

```typescript
import { generateUlid } from "@digicroz/ulid";

const ids = [];
ids.push(generateUlid());
await sleep(10);
ids.push(generateUlid());
await sleep(10);
ids.push(generateUlid());

// Sort naturally
ids.sort();
// IDs are now in chronological order!
```

### URL-safe Identifiers

Use ULIDs in URLs without encoding:

```typescript
import { generateUlid } from "@digicroz/ulid";

const articleId = generateUlid();
const url = `https://example.com/articles/${articleId}`;
// No URL encoding needed!
```

### Distributed Systems

ULIDs work great in distributed systems:

```typescript
import { generateUlid } from "@digicroz/ulid";

// Each server can generate unique IDs independently
// Server 1
const serverId1 = generateUlid();

// Server 2 (at the same time)
const serverId2 = generateUlid();

// IDs are guaranteed unique and sortable across servers
```

## Tree-shaking Support

Import only what you need for optimal bundle size:

```typescript
// Import specific functions
import { generateUlid } from "@digicroz/ulid/ulid";

// Or import from main entry
import { generateUlid, isValidUlid } from "@digicroz/ulid";
```

## TypeScript Configuration

For optimal compatibility, ensure your `tsconfig.json` uses modern module resolution:

```json
{
  "compilerOptions": {
    "moduleResolution": "bundler", // or "node16"/"nodenext"
    "module": "ESNext",
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "skipLibCheck": true
  }
}
```

## ULID Specification

This library implements the [ULID specification](https://github.com/ulid/spec):

```
 01AN4Z07BY      79KA1307SR9X4MV3

|----------|    |----------------|
 Timestamp          Randomness
   48bits             80bits
```

- **Timestamp**: 48-bit integer (millisecond precision)
- **Randomness**: 80-bit random number
- **Encoding**: Crockford's Base32 (case-insensitive)
- **Length**: 26 characters
- **Sortable**: Lexicographically by time

### Character Set

Uses Crockford's Base32 for better readability:

```
0123456789ABCDEFGHJKMNPQRSTVWXYZ
```

Excluded characters: I, L, O, U (to avoid confusion)

## Comparison with UUID

| Feature                  | ULID             | UUID v4                      |
| ------------------------ | ---------------- | ---------------------------- |
| **Length**               | 26 characters    | 36 characters (with hyphens) |
| **Sortable**             | ✅ Yes (by time) | ❌ No                        |
| **Monotonic**            | ✅ Yes           | ❌ No                        |
| **URL-safe**             | ✅ Yes           | ⚠️ Needs encoding            |
| **Case-sensitive**       | ❌ No            | ✅ Yes                       |
| **Timestamp**            | ✅ Embedded      | ❌ No                        |
| **Collision Resistance** | ✅ High          | ✅ High                      |
| **Binary Size**          | 128 bits         | 128 bits                     |

## Performance

- **Generation**: ~100,000 IDs per second (monotonic mode)
- **Validation**: ~1,000,000 validations per second
- **Binary Conversion**: ~500,000 conversions per second

## Browser Support

Works in all modern browsers:

- Chrome/Edge (latest)
- Firefox (latest)
- Safari (latest)
- React Native
- Web Workers

## Node.js Support

Requires Node.js 16.0.0 or higher.

## Development

```bash
# Install dependencies
npm install

# Build the project
npm run build

# Watch mode for development
npm run dev

# Clean build artifacts
npm run clean
```

## License

MIT © [Adarsh Hatkar](https://github.com/AdarshHatkar)

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Related Projects

- [ulid/spec](https://github.com/ulid/spec) - ULID specification
- [ulidx](https://github.com/perry-mitchell/ulidx) - Core ULID library used internally

## Support

For issues and feature requests, please use the [GitHub issue tracker](https://github.com/digicroz/ulid/issues).
