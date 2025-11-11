# Contributing to MCPMock Examples

Thank you for your interest in contributing to the MCPMock Examples repository! We welcome contributions from the community.

## How to Contribute

### Reporting Issues

If you find a bug or have a suggestion:

1. Check existing [issues](https://github.com/AratoAi/mcpmock/issues) first
2. Create a new issue with:
   - Clear title and description
   - Steps to reproduce (for bugs)
   - Expected vs actual behavior
   - Code examples if applicable

### Contributing Examples

We're always looking for new examples! To contribute:

1. **Fork the repository**
2. **Create a feature branch**: `git checkout -b feature/new-example`
3. **Add your example**:
   - Place it in the appropriate directory (`examples/basic`, `examples/advanced`, or `examples/integrations`)
   - Follow the existing format and structure
   - Include complete, working code samples
   - Add clear explanations and comments
4. **Test your example**: Verify all code works against mcpmock.ai
5. **Update README**: Add your example to the main README.md
6. **Commit your changes**: `git commit -m "Add example for [feature]"`
7. **Push to your fork**: `git push origin feature/new-example`
8. **Open a Pull Request**

### Example Structure

Each example should include:

```markdown
# Example Title

Brief description of what this example demonstrates.

## When to Use

When this pattern is useful.

## Code Example

```bash
# Complete working example
curl -X POST https://api.mcpmock.ai/tools \
  -H "Authorization: Bearer YOUR_TOKEN" \
  ...
```

## Explanation

Step-by-step explanation of the code.

## Output

Expected output or result.

## Next Steps

Links to related examples.
```

### Tutorial Contributions

For tutorial contributions:

1. Place in the `tutorials/` directory
2. Include:
   - Clear learning objectives
   - Prerequisites
   - Step-by-step instructions
   - Complete working examples
   - Common pitfalls and solutions
   - Summary and next steps

### Code Style Guidelines

**Markdown:**
- Use headers hierarchically (h1, h2, h3)
- Include code fences with language specification
- Keep lines under 120 characters when possible
- Use bullet points and numbered lists appropriately

**Code Examples:**
- Include complete, working examples
- Use realistic variable names
- Add comments for complex logic
- Show both request and response
- Handle errors appropriately

**Bash/curl:**
```bash
# Clear comment explaining the command
```bash
curl -X POST https://api.mcpmock.ai/tools \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "example_tool",
```

**JavaScript:**
```javascript
// Use clear variable names
const authToken = localStorage.getItem('authToken');

// Include error handling
try {
  const response = await fetch(url, options);
  if (!response.ok) {
    throw new Error(`HTTP ${response.status}`);
  }
  return await response.json();
} catch (error) {
  console.error('Error:', error.message);
}
```

### Integration Examples

For new integration examples (Python, Node.js, etc.):

1. Create directory: `examples/integrations/[language-or-framework]`
2. Include:
   - Complete working code
   - Dependency installation instructions
   - Configuration steps
   - Running instructions
   - Example output
3. Test thoroughly before submitting

### Documentation Updates

For documentation improvements:

1. Fix typos and grammar
2. Add clarifications
3. Update outdated information
4. Add missing details
5. Improve code examples

### Review Process

Pull requests will be reviewed for:

1. **Accuracy**: Code must work correctly
2. **Clarity**: Easy to understand and follow
3. **Completeness**: All necessary information included
4. **Consistency**: Matches existing style and structure
5. **Value**: Adds useful information to the repository

### Getting Help

- **Questions**: Open an [issue](https://github.com/AratoAi/mcpmock/issues) with the "question" label
- **Discussions**: Use [GitHub Discussions](https://github.com/AratoAi/mcpmock/discussions) for general topics
- **Documentation**: Check the [main repository](https://github.com/AratoAi/mcp-mock-server)

## Example Ideas

We're looking for examples in these areas:

- **Testing frameworks**: Jest, Pytest, Mocha integration
- **CI/CD**: GitHub Actions, GitLab CI examples
- **Languages**: Python, JavaScript, Java, Go, Ruby clients
- **Use cases**: AI agent testing, API prototyping, integration testing
- **Advanced patterns**: State machines, workflows, complex validations
- **Real-world scenarios**: E-commerce, IoT, microservices

## Code of Conduct

- Be respectful and constructive
- Welcome newcomers
- Focus on the content, not the person
- Assume good intentions
- No harassment or discrimination

## License

By contributing, you agree that your contributions will be licensed under the MIT License (for this examples repository).

---

Thank you for contributing to MCPMock! Your examples help developers around the world build better applications.

**Questions?** Open an issue or reach out through [GitHub Discussions](https://github.com/AratoAi/mcpmock/discussions).
