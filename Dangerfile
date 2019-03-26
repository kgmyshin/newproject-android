# Warn when there is a big PR
warn("a large PR") if git.lines_of_code > 1000

### lint ###

Dir.glob("**/lint-results.xml").each { |report|
  android_lint.filtering = true
  android_lint.report_file = report.to_s
  android_lint.lint(inline_mode: true)
}

### ktlint ###

# そのままのレポートだとerrorを検出したソースのファイルパスが `src/main/yourpackage/Source.kt` となっていて、
# モジュールまでのパスのことが考慮されていない。
# ファイルパスを`module_path/src/main/yourpackage/Source.kt`に変更したreportも作成して
# そちらをdangerの対象にする
require 'rexml/document'
Dir.glob("**/ktlint/*.xml").each { |report_file_path|
  report_file = File.new(report_file_path)
  module_path = report_file_path.slice(/(.*)\/build/, 1)
  doc = REXML::Document.new(report_file)
  doc.elements.each("/checkstyle/file") { |element|
    element.attributes["name"] = module_path + "/" + element.attributes["name"]
  }
  File.write(report_file_path.sub(".xml", "_path_changed.xml"), doc)
}

checkstyle_format.base_path = Dir.pwd
Dir.glob("**/ktlint/*_path_changed.xml").each { |report|
  checkstyle_format.report report.to_s
}
