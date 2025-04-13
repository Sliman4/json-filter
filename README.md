# JSON Filter

This crate allows checking if `serde_json::Value` matches a filter (which is JSON serializable too).

Examples:

```rust
    use serde_json::json;
    use json_filter::{Filter, Operator};

    #[test]
    fn test_numeric_operators() {
        let value = json!({ "age": 25 });

        let filter = Filter::new("age", Operator::GreaterThan(20.0));
        assert!(filter.check(&value).unwrap());

        let filter = Filter::new("age", Operator::LessThan(30.0));
        assert!(filter.check(&value).unwrap());

        let filter = Filter::new("age", Operator::GreaterOrEqual(25.0));
        assert!(filter.check(&value).unwrap());

        let filter = Filter::new("age", Operator::LessOrEqual(25.0));
        assert!(filter.check(&value).unwrap());
    }

    #[test]
    fn test_string_operators() {
        let value = json!({ "name": "John Doe" });

        let filter = Filter::new("name", Operator::StartsWith("John".to_string()));
        assert!(filter.check(&value).unwrap());

        let filter = Filter::new("name", Operator::EndsWith("Doe".to_string()));
        assert!(filter.check(&value).unwrap());

        let filter = Filter::new("name", Operator::Contains("hn D".to_string()));
        assert!(filter.check(&value).unwrap());
    }

    #[test]
    fn test_array_operators() {
        let value = json!({ "tags": ["rust", "coding", "json"] });

        let filter = Filter::new("tags", Operator::ArrayContains(json!("rust")));
        assert!(filter.check(&value).unwrap());

        let filter = Filter::new("tags[1]", Operator::Equals(json!("coding")));
        assert!(filter.check(&value).unwrap());
    }

    #[test]
    fn test_object_operators() {
        let value = json!({
            "user": {
                "id": 123,
                "details": {
                    "email": "john@example.com"
                }
            }
        });

        let filter = Filter::new("user", Operator::HasKey("id".to_string()));
        assert!(filter.check(&value).unwrap());

        let filter = Filter::new(
            "user.details.email",
            Operator::EndsWith("@example.com".to_string()),
        );
        assert!(filter.check(&value).unwrap());
    }

    #[test]
    fn test_logical_operators() {
        let value = json!({
            "age": 25,
            "name": "John Doe"
        });

        let filter = Filter::new(
            ".",
            Operator::And(vec![
                Filter::new("age", Operator::GreaterThan(20.0)),
                Filter::new("name", Operator::StartsWith("John".to_string())),
            ]),
        );
        assert!(filter.check(&value).unwrap());

        let filter = Filter::new(
            ".",
            Operator::Or(vec![
                Filter::new("age", Operator::GreaterThan(30.0)),
                Filter::new("name", Operator::Contains("John".to_string())),
            ]),
        );
        assert!(filter.check(&value).unwrap());
    }

```
