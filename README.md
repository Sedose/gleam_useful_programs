# gleam_useful_programs
Collection of useful scripts for day to day programmer (especially Java and Kotlin) job
# Gleam Scripts

## copy_bootstrap

Copies `bootstrap.yml` to `bootstrap-{profile}.yml` for each Spring Boot profile.

**What it does:**
- Finds all `application-*.yml` files in resources directory
- Extracts profile names (e.g., `application-dev.yml` → `dev`)
- Creates `bootstrap-dev.yml`, `bootstrap-prod.yml`, etc. from `bootstrap.yml`

**Why:** Spring Boot needs separate bootstrap files per profile for proper configuration loading.
It is suitable for legacy approaches that Microsoft libraries love to do relying on legacy Spring bootstrap mechanism.

**Usage:**
```bash
gleam run -m copy_bootstrap
```

**Example:**
```
Before:
├── bootstrap.yml
├── application-dev.yml
├── application-prod.yml

After:
├── bootstrap.yml
├── bootstrap-dev.yml      ← copied
├── bootstrap-prod.yml     ← copied
├── application-dev.yml
├── application-prod.yml
```

**src/copy_bootstrap.gleam**
```Gleam
import gleam/list
import gleam/string
import simplifile

const base_path = "C:/Users/Here_Comes_Your_User_Name/IdeaProjects/here-comes-your-repo-name/src/main/resources/"

pub fn main() {
  let assert Ok(files) = simplifile.read_directory(base_path)
  files
  |> get_profile_names
  |> copy_bootstrap_files
}

fn get_profile_names(files: List(String)) -> List(String) {
  files
  |> list.filter(is_application_profile_file)
  |> list.map(extract_profile_name)
}

fn is_application_profile_file(file: String) -> Bool {
  string.starts_with(file, "application-") && string.ends_with(file, ".yml")
}

fn extract_profile_name(file: String) -> String {
  file
  |> string.replace("application-", "")
  |> string.replace(".yml", "")
}

fn copy_bootstrap_files(profiles: List(String)) -> Nil {
  list.each(profiles, fn(profile) {
    let src = base_path <> "bootstrap.yml"
    let dst = base_path <> "bootstrap-" <> profile <> ".yml"
    simplifile.copy_file(src, dst)
  })
}
```
