# Scenario: Inter-VLAN Traffic Denied

## Goal
Verify firewall enforcement of network segmentation.

## Misconfiguration
Inter-VLAN traffic blocked due to default deny policy.

## Detection
Firewall logs indicated dropped packets between VLAN 10 and VLAN 20.

## Resolution
Confirmed deny rule was functioning as intended.

## Lesson Learned
Segmentation effectively limits lateral movement and reduces attack surface.
