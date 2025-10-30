+++
author = "Richard West"
title = "Tornado Tracks 2025 - preliminary take"
date = "2025-10-29"
description = "Tornado Track of 2025"
+++

Tornado track visible in MGRS tile 16SGG on 2025-07-23.

Examine this observation interactively on GEE [link](https://code.earthengine.google.com/dd60c27ccc7b378f5195205c06505f56).
Select the last tile (16SGG) from the tile pulldown and
zoom into the lower left area.
The images below show different layers from the GEE page.
The first image is an optical view showing the tornado track as
an obvious color shift where the Daniel Boone national forest was
severely damaged.

{{< rawhtml >}}
<figure>
  <img src="" id="hls1" alt="optical image of tornado track">
  <figcaption>Sentinel-2 7-day post Dist-S1 Observation.</figcaption>
</figure>
<script>
document.addEventListener("DOMContentLoaded", () => {
  const pathPrefix = window.location.pathname.includes('/dist-s1-blog/') ? '/dist-s1-blog' : '';
  const postUrl = `${pathPrefix}/posts/tornado_tracks/hls1.png`;
  const image = document.getElementById("hls1");
  if (image) {
    image.src = postUrl;
  } else {
    console.error("Image element not found");
  }
});
</script>
{{< /rawhtml >}}

The image below shows the Sentinel 1 radar image using VV polarization.
The tornado track is not obvious.
Close examination shows some subtle textural change along the track.

{{< rawhtml >}}
<figure>
  <img src="" id="s1obs" alt="radar image of tornado track">
  <figcaption>S1 Obs (VV).</figcaption>
</figure>
<script>
document.addEventListener("DOMContentLoaded", () => {
  const pathPrefix = window.location.pathname.includes('/dist-s1-blog/') ? '/dist-s1-blog' : '';
  const postUrl = `${pathPrefix}/posts/tornado_tracks/s1obs.png`;
  const image = document.getElementById("s1obs");
  if (image) {
    image.src = postUrl;
  } else {
    console.error("Image element not found");
  }
});
</script>
{{< /rawhtml >}}

The image below shows the Sentinel 1 VV radar image after subtracting off
a historical baseline in dB terms.
The track shows more clearly but still subtle as
a textural change.

{{< rawhtml >}}
<figure>
  <img src="" id="s1obs_rel" alt="radar relative image of tornado track">
  <figcaption>S1 VV OBS-BL.</figcaption>
</figure>
<script>
document.addEventListener("DOMContentLoaded", () => {
  const pathPrefix = window.location.pathname.includes('/dist-s1-blog/') ? '/dist-s1-blog' : '';
  const postUrl = `${pathPrefix}/posts/tornado_tracks/s1obs_rel.png`;
  const image = document.getElementById("s1obs_rel");
  if (image) {
    image.src = postUrl;
  } else {
    console.error("Image element not found");
  }
});
</script>
{{< /rawhtml >}}

The image below shows the corresponding Dist-S1 status labels.
The tornado track shows up sparsely.

{{< rawhtml >}}
<figure>
  <img src="" id="s1status" alt="Dist-S1 status image of tornado track">
  <figcaption>DIST-S1: GEN-STATUS.</figcaption>
</figure>
<script>
document.addEventListener("DOMContentLoaded", () => {
  const pathPrefix = window.location.pathname.includes('/dist-s1-blog/') ? '/dist-s1-blog' : '';
  const postUrl = `${pathPrefix}/posts/tornado_tracks/s1status.png`;
  const image = document.getElementById("s1status");
  if (image) {
    image.src = postUrl;
  } else {
    console.error("Image element not found");
  }
});
</script>
{{< /rawhtml >}}

Radar sees the tornado path less clearly than optical.
The tornado caused extensive damage to the forest, but the actual
density of scattering centers seems to be more re-arranged rather
than reduced.
