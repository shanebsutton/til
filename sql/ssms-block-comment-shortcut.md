# SSMS Block Comment Shortcut

I came across a post on [dba.stackexchange.com](https://dba.stackexchange.com/questions/147165/ssms-shortcut-to-comment-uncomment-a-specific-part-of-the-query/147182#147182) that showed a way to add block comments, `/*` and `*/`, using '[Surround-with snippets](https://docs.microsoft.com/en-us/sql/ssms/scripting/insert-surround-with-transact-sql-snippets)' in SSMS.

This will comment out an entire text selection using the Slash Star block comments, rather than using line-by-line `--` comments.

One thing to note is that there is no way to toggle the selection off...with the exception of using `ctrl + z` to undo the last edit.  So this may not be as useful as the built in 'Comment Selection' shortcut, `ctrl + k, ctrl + c`.  Depends on the situation...

The .snippet file (see the post for instructions/links on how to implement):

```xml
<?xml version="1.0" encoding="utf-8" ?>
<CodeSnippets  xmlns="http://schemas.microsoft.com/VisualStudio/2005/CodeSnippet">
    <_locDefinition xmlns="urn:locstudio">
        <_locDefault _loc="locNone" />
        <_locTag _loc="locData">Title</_locTag>
        <_locTag _loc="locData">Description</_locTag>
        <_locTag _loc="locData">Author</_locTag>
        <_locTag _loc="locData">ToolTip</_locTag>
        <_locTag _loc="locData">Default</_locTag>
    </_locDefinition>
    <CodeSnippet Format="1.0.0">
        <Header>
            <Title>Comment Block (surrounds)</Title>
            <Shortcut></Shortcut>
            <Description>Surround selected text with slash star comment block /**/</Description>
            <Author>wBob - dba.stackexchange.com</Author>
            <SnippetTypes>
                <SnippetType>SurroundsWith</SnippetType>
            </SnippetTypes>
        </Header>
        <Snippet>
            <Declarations>
                <Literal>
                    <!--<ID>x</ID><ToolTip>y</ToolTip><Default>z</Default>-->
                </Literal>
            </Declarations>
            <Code Language="SQL">
                <![CDATA[/* $selected$ $end$ */]]>
            </Code>
        </Snippet>
    </CodeSnippet>
</CodeSnippets>
```

Note that this can be edited to add newlines before and/or after the comment block.

## References

- [dba.stackexchange.com](https://dba.stackexchange.com/questions/147165/ssms-shortcut-to-comment-uncomment-a-specific-part-of-the-query/147182#147182)
- [SQL Docs - Add Transact SQL Snippets](https://docs.microsoft.com/en-us/sql/ssms/scripting/add-transact-sql-snippets)
