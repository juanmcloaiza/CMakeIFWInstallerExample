cmake_minimum_required(VERSION 3.5)

# Specify language NONE to skip enabling any languages
project(CMakeIFWInstallerExample NONE)

# Enable IFW generator
set(CPACK_GENERATOR IFW)

# Default Installation and Start Menu Directories
set(CPACK_IFW_PACKAGE_START_MENU_DIRECTORY "A Package Istallation with IFW")
set(CPACK_IFW_TARGET_DIRECTORY "@HomeDir@/${CMAKE_PROJECT_NAME}")

# If not set, all components will be merged in the group
set(CPACK_COMPONENTS_GROUPING IGNORE)

# The group, which will be used to configure the root package
set(CPACK_IFW_PACKAGE_GROUP pg)

#To use functions from CPack and CPackIFW
include(CPack)
include(CPackIFW)

# Add parent group
cpack_add_component_group(pg EXPANDED)
cpack_ifw_configure_component_Group(pg NAME my.ifw.installer)

# Add sub-groups
foreach(sg sg1 sg2 sg3)
  cpack_add_component_group(${sg} PARENT_GROUP pg)
  cpack_ifw_configure_component_group(${sg} NAME my.ifw.installer.${sg})

  # Add components to each group: install-add-configure
  # In order to add the start menu entry, an external configuration "qs script" is needed
  foreach(comp comp1 comp2 comp3)
    file(WRITE "${CMAKE_BINARY_DIR}/${sg}/${comp}.txt" "${sg}, ${comp}.txt contents\n")
    install(FILES "${CMAKE_BINARY_DIR}/${sg}/${comp}.txt"
            DESTINATION ${sg}
            COMPONENT ${sg}_${comp})
    cpack_add_component(${sg}_${comp}
                        DISABLED
                        GROUP ${sg})
    cpack_ifw_configure_component(${sg}_${comp} NAME org.foo.installer.${sg}.${comp} SCRIPT installscript_${sg}_${comp}.qs)
  endforeach()

endforeach()
