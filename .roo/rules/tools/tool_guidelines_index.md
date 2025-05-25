# Tool Usage Guidelines Index

To prevent common errors when using tools, refer to these detailed guidelines:

## File Operations
- [File Operations Guidelines](./file_operations.md) - Guidelines for read_file, write_to_file, and list_files

## Code Editing
- [Search and Replace Guidelines](./search_replace.md) - Guidelines for search_and_replace
- [Insert Content Guidelines](./insert_content.md) - Guidelines for insert_content

## Common Error Prevention
- [apply_diff Error Prevention](./apply_diff.md) - Specific guidelines to prevent errors with apply_diff

## KEY POINTS TO REMEMBER:
1. Always include all required parameters for each tool
2. Verify file existence before attempting modifications
3. For apply_diff, never include literal diff markers in code examples
4. For search_and_replace, always include both search and replace parameters
5. For write_to_file, always include the line_count parameter
6. For insert_content, always include valid start_line and content in operations array
